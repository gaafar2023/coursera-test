# PART 12 — Database Design

> Status: Draft v0.1 · 2026-09-25
> Engine: PostgreSQL (Supabase, D-026). IDs are `uuid` unless noted, and timestamps are `timestamptz` (UTC).
> The SQL below is a **specification** for review, not final migration code.

---

## 12.1 Entity overview

```
auth.users 1─1 profiles ─┬─< user_clubs >── clubs >── club_competition_seasons ──< competition_seasons >── competitions
                         │                                                              │
                         ├─< predictions >── fixtures ─< fixture_results (versioned)    │
                         │        │                 └──< fixtures belong to competition_seasons
                         │        └─< prediction_revisions (append-only)
                         │        └─< prediction_settlements (versioned) ──> scoring_rulesets
                         ├─< points_ledger (append-only) ──> leaderboard_entries (aggregates)
                         ├─< xp_ledger ── user_achievements >── achievements
                         ├─< group_members >── groups ─< group_invites
                         ├─< subscriptions >── subscription_plans ─< plan_entitlements >── entitlements
                         ├─< store_events (raw webhooks)
                         ├─< devices ─ notification_preferences ─< notification_outbox
                         └─< audit_log · moderation_actions · reports
countries ─< profiles, clubs            provider_payloads (raw ingestion)      feature_flags
```

## 12.2 Reference data

```sql
countries (code char(2) PK, name_en text NOT NULL, name_ar text NOT NULL)

competitions (
  id uuid PK, provider_ref text UNIQUE NOT NULL,          -- e.g. provider league id
  name_en text NOT NULL, name_ar text NOT NULL, short_code text NOT NULL,
  country_code char(2) REFERENCES countries,              -- NULL for continental (UCL)
  type text CHECK (type IN ('league','cup','continental')),
  lock_offset_minutes int NOT NULL DEFAULT 0 CHECK (lock_offset_minutes BETWEEN 0 AND 120),
  enabled boolean NOT NULL DEFAULT false, sort_order int)

competition_seasons (
  id uuid PK, competition_id uuid REFERENCES competitions, label text,  -- '2026/27'
  starts_on date, ends_on date, scoring_ruleset_id uuid REFERENCES scoring_rulesets NOT NULL,
  UNIQUE (competition_id, label))

clubs (
  id uuid PK, provider_ref text UNIQUE NOT NULL,
  name_en text NOT NULL, name_ar text NOT NULL, short_code char(3) NOT NULL,
  color_primary char(7) NOT NULL, color_secondary char(7) NOT NULL,     -- generated badge (D-006)
  country_code char(2) REFERENCES countries, logo_url text NULL)        -- only used if licensed (flag)

club_competition_seasons (club_id, competition_season_id, PRIMARY KEY (club_id, competition_season_id))
```

## 12.3 Users & preferences

```sql
profiles (
  user_id uuid PK REFERENCES auth.users ON DELETE CASCADE,
  username citext UNIQUE NOT NULL,                -- 3–20 chars: Arabic/Latin letters, digits, underscore (validated in RPC)
  country_code char(2) REFERENCES countries,
  locale text NOT NULL DEFAULT 'ar' CHECK (locale IN ('ar','en')),
  time_zone text NOT NULL,                       -- IANA, e.g. 'Asia/Riyadh'
  age_confirmed_16_plus boolean NOT NULL,        -- store the flag, not the full birth date (minimisation)
  avatar_path text, level int NOT NULL DEFAULT 1, xp_total bigint NOT NULL DEFAULT 0,
  status text NOT NULL DEFAULT 'active' CHECK (status IN ('active','suspended','banned','deleted')),
  leaderboard_eligible boolean NOT NULL DEFAULT true,     -- false while flagged by anti-cheat
  created_at timestamptz NOT NULL DEFAULT now())

user_competitions (user_id, competition_id, PRIMARY KEY (user_id, competition_id))

user_clubs (
  user_id uuid REFERENCES profiles, club_id uuid REFERENCES clubs,
  is_primary boolean NOT NULL DEFAULT false, PRIMARY KEY (user_id, club_id))
CREATE UNIQUE INDEX one_primary_club ON user_clubs (user_id) WHERE is_primary;

primary_club_history (             -- enforces "change once per season" (Part 8)
  id bigserial PK, user_id uuid REFERENCES profiles, club_id uuid REFERENCES clubs,
  season_label text NOT NULL, valid_from timestamptz NOT NULL, valid_to timestamptz NULL)
```

## 12.4 Fixtures & results

```sql
fixtures (
  id uuid PK, provider_ref text UNIQUE NOT NULL,
  competition_season_id uuid NOT NULL REFERENCES competition_seasons,
  round_label text, game_week date NOT NULL,              -- Tuesday-start week (D-021)
  home_club_id uuid NOT NULL REFERENCES clubs, away_club_id uuid NOT NULL REFERENCES clubs,
  kickoff_at timestamptz NOT NULL,
  lock_at timestamptz NOT NULL,                           -- kickoff_at − lock_offset; recomputed by ingestion
  status text NOT NULL CHECK (status IN ('scheduled','live','finished','postponed','cancelled',
                                         'abandoned','suspended','awarded','hold')),
  settlement_state text NOT NULL DEFAULT 'pending'
        CHECK (settlement_state IN ('pending','settled','void','frozen')),
  is_derby boolean NOT NULL DEFAULT false,
  current_result_version int NOT NULL DEFAULT 0,
  updated_at timestamptz NOT NULL DEFAULT now(),
  CHECK (home_club_id <> away_club_id))
CREATE INDEX fixtures_by_week   ON fixtures (competition_season_id, game_week);
CREATE INDEX fixtures_by_kickoff ON fixtures (kickoff_at);
CREATE INDEX fixtures_open      ON fixtures (lock_at) WHERE status = 'scheduled';

fixture_results (                                          -- every result version is kept
  fixture_id uuid REFERENCES fixtures, version int,
  reg_home smallint NOT NULL CHECK (reg_home >= 0),       -- 90 min + stoppage (Part 7)
  reg_away smallint NOT NULL CHECK (reg_away >= 0),
  final_home smallint, final_away smallint,               -- incl. ET (display only)
  pen_home smallint, pen_away smallint,                    -- display only
  source text NOT NULL CHECK (source IN ('provider','admin')),
  result_hash text NOT NULL, reason text,                 -- reason required when source = 'admin'
  recorded_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (fixture_id, version))

provider_payloads (                                        -- raw ingestion, for replay/debug (retention 90 days)
  id bigserial PK, provider text, endpoint text, fetched_at timestamptz, body jsonb, body_hash text,
  UNIQUE (provider, endpoint, body_hash))
```

## 12.5 Predictions (integrity core)

```sql
predictions (                                             -- current value; one per user per fixture
  user_id uuid REFERENCES profiles, fixture_id uuid REFERENCES fixtures,
  home smallint NOT NULL CHECK (home BETWEEN 0 AND 15),
  away smallint NOT NULL CHECK (away BETWEEN 0 AND 15),
  first_submitted_at timestamptz NOT NULL,               -- server time
  last_submitted_at timestamptz NOT NULL,                -- server time
  revision_count int NOT NULL DEFAULT 1,
  locked_at timestamptz NULL,                             -- set by the lock job = fixture.lock_at
  state text NOT NULL DEFAULT 'open' CHECK (state IN ('open','locked','settled','void')),
  PRIMARY KEY (user_id, fixture_id))
CREATE INDEX predictions_by_fixture ON predictions (fixture_id);   -- settlement scan

prediction_revisions (                                    -- APPEND-ONLY: UPDATE/DELETE revoked
  id bigserial PK, user_id uuid, fixture_id uuid,
  revision int NOT NULL, home smallint NOT NULL, away smallint NOT NULL,
  submitted_at timestamptz NOT NULL DEFAULT now(),        -- DB clock
  idempotency_key uuid NOT NULL,
  client_meta jsonb,                                      -- app version, platform, attestation verdict (no device clock trust)
  UNIQUE (user_id, fixture_id, revision),
  UNIQUE (user_id, idempotency_key),
  FOREIGN KEY (user_id, fixture_id) REFERENCES predictions)

scoring_rulesets (
  id uuid PK, code text UNIQUE NOT NULL, version int NOT NULL,
  rules jsonb NOT NULL,                                   -- {"exact":5,"gd":3,"result":2}
  created_at timestamptz NOT NULL DEFAULT now())           -- immutable once referenced

prediction_settlements (                                  -- one row per (prediction, result version)
  user_id uuid, fixture_id uuid, result_version int,
  ruleset_id uuid REFERENCES scoring_rulesets,
  outcome text NOT NULL CHECK (outcome IN ('exact','gd','result','wrong','void')),
  points smallint NOT NULL CHECK (points >= 0),
  settled_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (user_id, fixture_id, result_version),
  FOREIGN KEY (fixture_id, result_version) REFERENCES fixture_results)

points_ledger (                                           -- APPEND-ONLY; the source of all leaderboards
  id bigserial PK, user_id uuid NOT NULL, fixture_id uuid NOT NULL,
  result_version int NOT NULL, delta smallint NOT NULL,   -- corrections post +/- deltas
  competition_id uuid NOT NULL, game_week date NOT NULL, season_label text NOT NULL,
  club_id uuid NULL,                                      -- primary club at the time of settlement
  created_at timestamptz NOT NULL DEFAULT now(),
  UNIQUE (user_id, fixture_id, result_version))           -- idempotent settlement
CREATE INDEX ledger_by_user ON points_ledger (user_id, created_at);
```

**Integrity guarantees (enforced in the database, not just in the app):**
1. Only the `submit_prediction` RPC may write to `predictions` and `prediction_revisions`. Direct table writes are denied by RLS.
2. The trigger `predictions_block_after_lock` raises an error if `now() >= fixtures.lock_at` on INSERT/UPDATE, unless the fixture was reopened (§7.2).
3. `REVOKE UPDATE, DELETE` on `prediction_revisions`, `points_ledger`, `audit_log` and `store_events` from **all** roles, the service role included. Mistakes are fixed with new rows.
4. The unique keys make settlement and submission idempotent.
5. Together, these records give a permanent answer to the questions the brief requires us to track:
   - what was predicted: `prediction_revisions.home/away`
   - when it was submitted: `submitted_at`
   - when it locked: `predictions.locked_at` / `fixtures.lock_at`
   - the original prediction: revision 1
   - every change before lock: revisions 2..n
   - how it was scored: `prediction_settlements.outcome`
   - the points awarded: `points`
   - the settlement version: `result_version` + `ruleset_id`

## 12.6 Leaderboards

```sql
leaderboard_entries (                                     -- incremental aggregates, rebuildable from the ledger
  scope text CHECK (scope IN ('global','country','competition','club_fans','group')),
  scope_id text NOT NULL,                                 -- 'all' | country code | competition id | club id | group id
  period text CHECK (period IN ('week','month','season')), period_key text NOT NULL,  -- '2026-W40' | '2026-10' | '2026/27'
  user_id uuid NOT NULL,
  points int NOT NULL DEFAULT 0, exact_count int NOT NULL DEFAULT 0, result_count int NOT NULL DEFAULT 0,
  predictions_settled int NOT NULL DEFAULT 0,
  PRIMARY KEY (scope, scope_id, period, period_key, user_id))
CREATE INDEX lb_rank ON leaderboard_entries (scope, scope_id, period, period_key,
                                             points DESC, exact_count DESC, result_count DESC);

club_rankings (                                           -- club vs club (Part 8 §8.5), recomputed after each settlement batch
  period text, period_key text, club_id uuid, active_fans int, mean_points numeric(6,2),
  eligible boolean, computed_at timestamptz, PRIMARY KEY (period, period_key, club_id))

rank_snapshots (scope, scope_id, period, period_key, user_id, rank int, taken_at timestamptz)  -- for ▲▼ movement
```
Group boards use `scope = 'group'`. Custom group scoring (Pro) writes to a separate `group_points_ledger`, which never touches global scopes (the §6.4 invariant).

## 12.7 Gamification

```sql
achievements (id text PK, category text, tier text, name_en text, name_ar text,
              criteria jsonb NOT NULL, xp_reward int NOT NULL, active boolean)
user_achievements (user_id uuid, achievement_id text REFERENCES achievements,
                   unlocked_at timestamptz NOT NULL, source_fixture_id uuid NULL,
                   PRIMARY KEY (user_id, achievement_id))
xp_ledger (id bigserial PK, user_id uuid, delta int NOT NULL, reason text NOT NULL,
           ref text, created_at timestamptz DEFAULT now(), UNIQUE (user_id, reason, ref))  -- idempotent
streaks (user_id uuid, kind text CHECK (kind IN ('participation','result','exact')),
         current int, longest int, last_event_key text, freezes_left int,
         PRIMARY KEY (user_id, kind))
challenges (V1.1) / user_challenges (V1.1)
```

## 12.8 Groups & social

```sql
groups (id uuid PK, name text NOT NULL CHECK (length(name) BETWEEN 3 AND 40),
        owner_id uuid REFERENCES profiles, max_members int NOT NULL,
        custom_ruleset_id uuid NULL REFERENCES scoring_rulesets,   -- Pro only
        competitions uuid[] NULL,                                  -- optional filter
        created_at timestamptz DEFAULT now(), archived_at timestamptz NULL)
group_members (group_id uuid REFERENCES groups ON DELETE CASCADE, user_id uuid REFERENCES profiles,
               role text CHECK (role IN ('owner','admin','member')), joined_at timestamptz,
               PRIMARY KEY (group_id, user_id))
CREATE INDEX group_members_by_user ON group_members (user_id);
group_invites (code text PK,                      -- 8 chars, unguessable, rate-limited lookups
               group_id uuid REFERENCES groups, created_by uuid, expires_at timestamptz,
               max_uses int, uses int DEFAULT 0, revoked boolean DEFAULT false)
friendships (V1.1: user_id, friend_id, status, created_at; PK (user_id, friend_id))
h2h_challenges (V1.1)
```

## 12.9 Subscriptions

```sql
subscription_plans (id text PK,                   -- 'plus_monthly','plus_annual','pro_monthly','pro_annual'
                    tier text CHECK (tier IN ('plus','pro')), period text, store_product_ids jsonb, active boolean)
entitlements (key text PK, description text)
plan_entitlements (tier text CHECK (tier IN ('free','plus','pro')), entitlement_key text REFERENCES entitlements,
                   value jsonb NOT NULL, PRIMARY KEY (tier, entitlement_key))   -- Part 6 §6.4 matrix
subscriptions (user_id uuid PK REFERENCES profiles, tier text NOT NULL DEFAULT 'free',
               plan_id text NULL REFERENCES subscription_plans, status text
               CHECK (status IN ('active','trial','grace','billing_retry','expired','refunded')),
               current_period_end timestamptz, store text, updated_at timestamptz)
store_events (id text PK,                         -- RevenueCat event id → idempotent webhook handling
              user_id uuid, type text, payload jsonb, received_at timestamptz)   -- APPEND-ONLY
```
We store **no card data**. Payments stay entirely with Apple and Google (PCI scope is minimal).

## 12.10 Notifications & devices

```sql
devices (id uuid PK, user_id uuid REFERENCES profiles ON DELETE CASCADE, platform text,
         fcm_token text UNIQUE, app_version text, last_seen_at timestamptz)
notification_preferences (user_id uuid PK, reminders bool, results bool, rank bool, groups bool,
                          club_vs_club bool, marketing bool DEFAULT false,
                          quiet_start time DEFAULT '00:00', quiet_end time DEFAULT '08:00')
notification_outbox (id bigserial PK, user_id uuid, category text, payload jsonb,
                     dedupe_key text UNIQUE, send_after timestamptz, sent_at timestamptz NULL, error text)
CREATE INDEX outbox_due ON notification_outbox (send_after) WHERE sent_at IS NULL;
notifications_inbox (id bigserial PK, user_id uuid, category text, title_key text, params jsonb,
                     deep_link text, created_at timestamptz, read_at timestamptz NULL)
```

## 12.11 Security, moderation & operations

```sql
audit_log (id bigserial PK, actor_id uuid, actor_role text, action text NOT NULL,
           target_type text, target_id text, reason text, before jsonb, after jsonb,
           ip inet, created_at timestamptz DEFAULT now())         -- APPEND-ONLY, retained 2 years
admin_roles (user_id uuid PK, role text CHECK (role IN ('support','operator','admin','superadmin')))
reports (id bigserial PK, reporter_id uuid, target_user_id uuid, reason text, status text, created_at timestamptz)
moderation_actions (id bigserial PK, target_user_id uuid, action text CHECK (action IN
                    ('warn','hide_from_boards','suspend','ban','restore')), reason text, actor_id uuid, created_at timestamptz)
feature_flags (key text PK, enabled boolean, rollout_percent int, rules jsonb, updated_by uuid, updated_at timestamptz)
```

## 12.12 Row-Level Security (summary)

| Table | Read | Write |
|---|---|---|
| Reference + fixtures + results | Everyone (authenticated) | Service role only (ingestion) |
| profiles | Public fields to everyone; private fields to the owner | Owner (limited columns), via RPC |
| predictions | Owner always; others **only after lock** (for group views) | `submit_prediction` RPC only |
| prediction_revisions | Owner | RPC only |
| leaderboard_entries, club_rankings | Everyone | Service role |
| groups / members | Members | Owner/admin via RPC |
| subscriptions | Owner | Webhook function only |
| audit_log, moderation | Admin roles | Security-definer functions only |

Showing other users' predictions only after lock stops copying and herding.

## 12.13 Data retention & deletion

| Data | Retention | On account deletion |
|---|---|---|
| Profile, devices, preferences | While the account is active | **Deleted** within 30 days |
| Predictions, revisions, settlements, ledger | Current season + 2 seasons (integrity/disputes), then aggregated | Re-keyed to a random tombstone ID with no link to the person, removed from all boards, and the username erased |
| Provider raw payloads | 90 days | n/a |
| Store events | 7 years **[counsel to confirm financial record rules]** | Pseudonymised |
| Audit log | 2 years | Actor IDs pseudonymised for deleted users |
| Analytics events | 14 months | Deletion request forwarded to the analytics vendor |

**Q-17 (counsel):** confirm that tombstoned prediction records count as anonymised under PDPL/GDPR, and confirm the retention periods.

## 12.14 Scale notes
- `prediction_revisions` and `points_ledger` are the fastest-growing tables. At ~1M users × 10 predictions/week they reach roughly 500M rows/year, so partition them by month (range) from the growth stage.
- Leaderboard pages are served from `leaderboard_entries` + the rank index. Move them to Redis sorted sets once p95 > 200 ms (D-028).
- All foreign-key columns used in joins are indexed. Queries are reviewed with `EXPLAIN` in CI for the 10 hottest queries.

# PART 6 — Feature Architecture

> Status: Draft v0.1 · 2026-09-24
> This part defines **what the product is made of** (functional modules and how they interact).
> Technology choices come in Part 11, and the data model in Part 12.

---

## 6.1 Functional modules

| # | Module | Responsibility | MVP | Owner persona |
|---|---|---|:-:|---|
| M1 | **Identity & Account** | Sign-up/in (Apple, Google, email), age gate, profile basics, account deletion, data export | ● | All |
| M2 | **Preferences** | Languages, leagues, clubs (primary + others), time zone, notification settings | ● | All |
| M3 | **Football Catalogue** | Competitions, seasons, clubs, fixtures, standings, basic form/H2H (from the data provider, cached) | ● | All |
| M4 | **Data Ingestion** | Pulls fixtures and results from the provider, normalises statuses, detects changes and corrections, emits domain events | ● | System |
| M5 | **Prediction** | Create/edit predictions before lock, server-side lock enforcement, immutable history | ● | All |
| M6 | **Settlement & Scoring** | Scores predictions against official results using versioned rules; handles voids and corrections | ● | System |
| M7 | **Leaderboards** | Global, country, club, club-vs-club and group rankings, each weekly and for the season | ● | All |
| M8 | **Groups** | Private groups, invite links/codes, membership, group boards, group custom scoring (Pro) | ● | Organizer |
| M9 | **Gamification** | XP, levels, streaks, badges (MVP: basic set); challenges (V1.1) | ◐ | All |
| M10 | **Notifications** | Push scheduling, user preferences, quiet hours, frequency caps | ● | All |
| M11 | **Subscriptions & Entitlements** | Store purchases, receipt validation, entitlements (feature → tier), restore, grace periods | ● | Paying users |
| M12 | **Sharing** | Result/prediction cards, deep links for invites | ◐ | Rival, Organizer |
| M13 | **Admin Console** | Fixture override, re-settlement, moderation, feature flags, audit viewing | ● | Operator |
| M14 | **Analytics & Observability** | Product events, crash reports, logs, metrics, alerts | ● | Team |
| M15 | **Localisation** | Strings AR/EN, RTL, number/date formatting, club/league names in both languages | ● | All |
| M16 | **Social graph** (friends, follow, H2H challenges) | V1.1 | – | Rival |
| M17 | **Content & Community** (comments, public groups) | V2 | – | – |

● = MVP · ◐ = partial in MVP · – = later

## 6.2 How the modules connect (event-driven core)

```
 Data Provider ──► [M4 Ingestion] ──emits──► MatchScheduled / KickoffChanged / MatchStatusChanged
                                             / MatchFinished / ResultCorrected
                                                   │
          ┌────────────────────────────────────────┼───────────────────────────────┐
          ▼                                        ▼                               ▼
   [M5 Prediction]                         [M6 Settlement]                  [M10 Notifications]
   recompute lock_at                        score predictions                 reminders, results
   reopen/void on postpone                  emits PredictionSettled
                                                   │
                           ┌───────────────────────┼─────────────────────┐
                           ▼                       ▼                     ▼
                    [M7 Leaderboards]        [M9 Gamification]     [M14 Analytics]
                    update sorted sets        XP, streaks, badges
                           │
                           ▼
                    RankChanged ──► [M10] weekly movement push
```

**Design rules**
1. **Ingestion is the only module that talks to the data provider.** Everything else consumes internal events. This makes a provider switch (or adding a second provider) a change to one module.
2. **Settlement is the only writer of points.** Leaderboards and gamification derive from settlement output, so they can always be rebuilt from it.
3. **Every event is idempotent**: it carries an ID and a version, and handlers tolerate duplicates.
4. **Entitlements are checked on the server**, never trusted from the app.

## 6.3 Key user flows

### F1 — First-run to first prediction (target < 60 s)
```
Splash → Language (AR default in KSA/UAE) → Pick leagues (multi) → Pick clubs (≥1, star = primary)
→ "Your next match: Al Hilal vs Al Ittihad — what's your score?" → stepper → [Submit]
→ Sign in with Apple/Google/email (the pick is held on the device until auth succeeds) → submitted → receipt
→ Soft prompt: "Get a reminder before kick-off?" → OS notification permission
```

### F2 — Group invite (growth loop)
```
Organizer: Groups → Create → name → [Share invite] → WhatsApp share sheet (link + code)
Invitee: taps link → (app installed?) opens group preview → Join
                  → (not installed) store → install → deferred deep link → onboarding (shortened) → auto-join group
```
Deferred deep linking is required so the invite survives the app install.

### F3 — Matchweek prediction
```
Home "Predict this matchweek (7 open)" → bulk list with steppers → Save all → per-match receipts
```

### F4 — Settlement & feedback
```
FT reported → settlement (≤15 min p95) → push "Full time: HIL 2–1 ITT. You predicted 2–1: EXACT +5"
→ open → breakdown + rank change + club contribution → [Share card]
```

### F5 — Upgrade
```
Contextual paywall (e.g. trying to create a 2nd group on Free) → Plus/Pro comparison → store purchase
→ server validates the receipt → entitlements refresh → the action continues where the user left off
```

## 6.4 Entitlements model (supports Free / Plus / Pro and future changes)

```
entitlement_key            free    plus    pro
ads.hidden                 false   true    true
groups.join.max            3       10      25      (placeholders, tuned in Part 9)
groups.create.max          1       3       10
groups.size.max            50      50      200
stats.personal.advanced    false   true    true
history.full               false   true    true
cosmetics.plus             false   true    true
cosmetics.pro              false   false   true
notifications.advanced     false   true    true
insights.prematch.deep     false   false   true
groups.custom_scoring      false   false   true
features.early_access      false   false   true
```
These values are stored in the database and editable in the admin console. The app asks the server "what can this user do?" and never hard-codes tiers.

**Integrity invariant:** no entitlement key may affect `points`, `lock_at`, or global/country/club leaderboard computation. This is enforced by code review and an automated test (Part 26).

## 6.5 Feature flags (MVP set)
`live_match_card`, `community_distribution`, `share_cards`, `achievements_basic`, `ads_enabled`,
`competition.<id>.enabled`, `logos.<competition>.enabled` (default off, D-006), `paywall.variant`.
They allow a staged rollout, emergency switch-off, and A/B tests without an app release.

## 6.6 Non-functional requirements (drive Part 11)

| Area | Requirement |
|---|---|
| Integrity | The server clock decides locks. Predictions are append-only with full history. Settlement is reproducible |
| Latency | Prediction submit p95 < 300 ms (in-region). Leaderboard read p95 < 200 ms |
| Settlement | ≤ 15 min p95 after the provider reports FT (D-008) |
| Peak load | Designed for a lock-time spike: e.g. 10× normal submit rate in the last 10 minutes before a derby kick-off (Part 27) |
| Availability | 99.5% monthly for MVP (lean budget); 99.9% target at scale |
| Localisation | All UI mirrored in RTL. No hard-coded strings |
| Privacy | Minimal personal data. Deletion within 30 days. Data export |
| Cost | Must run at low monthly cost at < 10k users (D-016) |

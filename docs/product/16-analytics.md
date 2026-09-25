# PART 16 — Analytics

> Status: Draft v0.1 · 2026-09-25
> Goals: measure the north star (**WPU**, Part 4), prove or disprove the MVP hypotheses (Part 2 A1–A7, Part 5 §5.5), and do it **privacy-first** (Part 17 §17.6).

---

## 16.1 Tool choice

| Option | Advantages | Disadvantages | Cost at our scale | Verdict |
|---|---|---|---|---|
| **PostHog (cloud)** | Product analytics + funnels + retention + feature-flag experiments + session replay in one tool. Open source (can self-host later) | Another vendor. Data-residency review needed | Generous free tier **[verify current limits]** | **✅ Recommended** |
| Firebase Analytics (GA4) | Free, integrates with Crashlytics/FCM | Weaker funnels and cohorts. Advertising-oriented data use needs a careful privacy review | Free | Keep **Crashlytics only** |
| Mixpanel / Amplitude | Strong analytics | Cost grows quickly | Free tiers exist | Not needed |

**Decision (D-034):** PostHog for product analytics, Crashlytics for crashes, and our own database for business truth (subscriptions, points).
- Session replay stays **off** in the MVP (privacy).
- IP addresses are not stored, and the user ID is a random analytics ID, never the email.
- An analytics consent toggle lives in Settings **[counsel: consent vs legitimate interest, Q-17 family]**.

## 16.2 Event taxonomy — conventions
- **Naming:** `object_action` in snake_case, past tense (`prediction_submitted`). Each event name is fixed forever; new meaning means a new event name.
- **Standard properties on every event:** `app_version`, `platform`, `locale`, `country`, `tier`, `session_id`, `is_first_session`, and `server_time` when relevant.
- **Tracking plan:** kept as a versioned file (`analytics/tracking-plan.yaml`) that CI checks against the Dart event constants, so no ad-hoc events reach production.
- **Server-side events** for anything that must be exact (settlement, subscriptions). Client events are for behaviour.

## 16.3 Event catalogue (MVP)

### Acquisition & onboarding
| Event | Key properties | Source |
|---|---|---|
| `app_opened` | `source` (organic, push, deep_link, invite) | client |
| `onboarding_started` | – | client |
| `language_selected` | `locale` | client |
| `league_selected` / `league_deselected` | `competition_id`, `position` | client |
| `club_selected` | `club_id`, `is_primary` | client |
| `onboarding_completed` | `leagues_count`, `clubs_count`, `duration_s` | client |
| `account_created` | `method` (apple, google, email), `had_pending_prediction` | server |
| `age_gate_blocked` | – (no age data) | client |
| `notification_permission_prompted` / `_granted` / `_denied` | `context` | client |
| `invite_link_opened` | `group_id`, `installed` (bool) | client |

### Prediction core
| Event | Key properties | Source |
|---|---|---|
| `fixture_list_viewed` | `date`, `competition_filter` | client |
| `match_viewed` | `fixture_id`, `competition_id`, `minutes_to_lock`, `is_favourite_club` | client |
| `prediction_started` | `fixture_id`, `entry_point` (home, matches, push, bulk) | client |
| `prediction_submitted` | `fixture_id`, `is_edit`, `revision`, `minutes_to_lock`, `bulk` | **server** |
| `prediction_rejected` | `fixture_id`, `reason` (locked, rate_limited, attestation) | server |
| `prediction_changed` | `fixture_id`, `revision`, `minutes_to_lock` | server |
| `prediction_locked` | `fixture_id`, `predictions_count` (per fixture, aggregate) | server |
| `prediction_settled` | `fixture_id`, `outcome` (exact, gd, result, wrong, void), `points`, `result_version` | server |
| `result_corrected` | `fixture_id`, `affected_users`, `version` | server |
| `result_viewed` | `fixture_id`, `outcome` | client |
| `share_card_created` / `share_card_shared` | `type`, `target_app` (if known) | client |
| `live_match_viewed` | `fixture_id` | client |

### Competition & gamification
| Event | Key properties | Source |
|---|---|---|
| `leaderboard_viewed` | `scope`, `period`, `rank_bucket` | client |
| `rank_changed` | `scope`, `from_bucket`, `to_bucket` | server |
| `achievement_unlocked` | `achievement_id`, `tier` | server |
| `level_up` | `level` | server |
| `streak_extended` / `streak_broken` / `streak_freeze_used` | `kind`, `length` | server |
| `coins_earned` / `coins_spent` (store V1.1) | `amount`, `reason` | server |

### Social
| Event | Key properties | Source |
|---|---|---|
| `group_created` | `group_id` | server |
| `group_invite_shared` | `group_id`, `channel` | client |
| `group_joined` | `group_id`, `via` (link, code) | server |
| `group_left` / `group_member_removed` | `group_id` | server |

### Monetization
| Event | Key properties | Source |
|---|---|---|
| `paywall_viewed` | `trigger` (groups_limit, stats, custom_scoring, store_item…), `variant` | client |
| `plan_selected` | `tier`, `period` | client |
| `purchase_started` / `purchase_failed` | `tier`, `period`, `error` | client |
| `trial_started` | `tier`, `period` | server (webhook) |
| `subscription_started` / `_renewed` / `_cancelled` / `_expired` / `_refunded` | `tier`, `period`, `is_trial_conversion` | server (webhook) |
| `subscription_upgraded` / `_downgraded` | `from`, `to` | server |

### Notifications & settings
| Event | Key properties | Source |
|---|---|---|
| `push_sent` | `category` | server |
| `push_opened` | `category` | client |
| `notification_setting_changed` | `category`, `enabled` | client |
| `analytics_consent_changed` | `enabled` | client |
| `account_deletion_requested` / `data_export_requested` | – | server |

## 16.4 Key funnels & reports

| Report | Definition | Answers |
|---|---|---|
| **Activation funnel** | `onboarding_started` → `onboarding_completed` → `prediction_submitted` (first) → `account_created` | Do users reach the first prediction within 60 s? (Part 3) |
| **Weekly habit** | WPU by week; % of week-1 predictors still predicting in week 4 | A1 (weekly habit) |
| **Viral loop** | `group_created` → `group_invite_shared` → `invite_link_opened` → `group_joined` → first prediction | A3; K-factor |
| **Club rivalry effect** | Retention of users who view club-vs-club boards vs those who don't | A2 |
| **Paywall** | `paywall_viewed` (by trigger) → `plan_selected` → `trial_started`/`subscription_started` | A4, the best upgrade moments |
| **Notification health** | Opens and opt-outs per category | Are we annoying people? |
| **Integrity** | Rejected-after-lock rate, correction rate, settlement lag | Trust (D-008) |

## 16.5 Experimentation
- Use PostHog feature flags + experiments for:
  - paywall variants
  - onboarding order
  - reminder timing
  - showing community distribution
- **Never experiment on scoring rules or lock rules.** Those must be the same for everyone (fairness).
- Minimum sample and duration are defined before each test starts. Results are recorded in the decision log.

## 16.6 Privacy rules for analytics
- No emails, usernames or free text in event properties. Rank and score values are bucketed where individual values aren't needed.
- The analytics user ID is a random ID mapped server-side. On account deletion, the analytics person is deleted through the vendor API.
- Retention: 14 months (Part 12 §12.13).
- Nothing leaves our analytics tool for advertising purposes.

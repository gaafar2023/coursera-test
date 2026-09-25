# PART 11 — Technical Architecture

> Status: Draft v0.1 · 2026-09-25
> Constraints: very small budget and no team (D-012, D-016). Server-authoritative integrity (Part 7). AR/EN RTL. ≤ 15 min settlement (D-008).
> The architecture must **start almost free and grow without a rewrite** from 1k to 1M+ users.

---

## 11.1 Mobile framework decision

| | **Flutter** | **React Native** | **Native (Swift + Kotlin)** |
|---|---|---|---|
| Codebases | 1 | 1 | 2 |
| Arabic RTL | Very consistent: Flutter draws its own UI, and `Directionality` mirrors layouts at framework level | Good. Uses the platform RTL engine; custom components and some third-party libraries need manual RTL work | Best platform fidelity, but twice the work |
| UI consistency iOS/Android | Identical rendering | Near-identical | Platform-specific |
| Performance for our UI (lists, steppers, animations) | Excellent | Very good (New Architecture) | Excellent |
| Ecosystem for our needs (push, IAP, deep links, auth) | Mature: `firebase_messaging`, `purchases_flutter`, `supabase_flutter`, `sign_in_with_apple` | Mature | Native SDKs |
| Solo / lean build cost | **Lowest** | Low | Highest |
| Hiring pool in GCC | Large **[assumption]** | Large | Smaller, and costlier |
| **Verdict** | **✅ Chosen** | Good alternative | Not justified |

**Decision (D-025): Flutter.** The main reasons are RTL consistency (a core requirement) and the lowest cost of one codebase. React Native would also work, and nothing in our requirements makes it a wrong choice.
Sources: [Droids On Roids](https://www.thedroidsonroids.com/blog/flutter-vs-react-native-comparison), [DEV Community (UAE agency)](https://dev.to/pythonista999/native-vs-react-native-vs-flutter-how-we-choose-a-mobile-stack-with-real-uae-cost-data-hja).

## 11.2 Backend approach decision

| Option | Advantages | Disadvantages | Monthly cost at MVP | Scalability | Verdict |
|---|---|---|---|---|---|
| **A. Supabase (managed Postgres + Auth + Storage + Edge Functions + cron) + TypeScript domain logic** | Almost no ops. Auth (Apple/Google/email) included. Postgres gives relational integrity for predictions and leaderboards. Open source, so it can be self-hosted later | Some vendor coupling (Auth, Edge Functions). Edge Functions have execution limits | $0 (free) → $25 (Pro) ([Supabase billing docs](https://supabase.com/docs/guides/platform/billing-on-supabase); plan details via [MakerKit](https://makerkit.dev/blog/saas/supabase-pricing)) **[verify]** | Good to ~100k+ WPU with Pro compute add-ons | **✅ Recommended for MVP** |
| B. Custom NestJS (or FastAPI) monolith + Postgres + Redis on a cloud provider | Full control, a classic scalable design | We must build auth, admin plumbing and deployment. Needs ops skills | ~$30–100+ | Excellent | Target shape **later**, if A's limits are reached |
| C. Firebase (Firestore + Cloud Functions) | Very fast to start, great push and analytics | NoSQL is a poor fit for leaderboards, joins and transactional settlement. Strong lock-in | $0 → usage-based | Good, but costly for reads-heavy leaderboards | ❌ Not for core data (we still use **FCM** for push) |

**Decision (D-026): Option A.** It is the only option that gives a solo, low-budget build production-grade integrity. Postgres is the source of truth, so moving to option B later means moving compute, not data.

**Language choice:** **TypeScript** for all server-side logic (Edge Functions, workers, admin web), and **Dart** on mobile.
Why not Python/FastAPI: it would work equally well, but TypeScript lets the domain logic (scoring, lock rules) be shared by the Edge Functions, the admin console and the test suites in one language.

## 11.3 System overview

```
                         ┌──────────────────────────── Mobile (Flutter, iOS + Android) ───────────────────────────┐
                         │ supabase_flutter · firebase_messaging · purchases_flutter (RevenueCat) · Crashlytics   │
                         └───────┬───────────────────────────┬──────────────────────────┬─────────────────────────┘
                     HTTPS (JWT) │                            │ push (FCM → APNs/Android) │ purchases (StoreKit/Play)
                                 ▼                            │                           ▼
┌──────────────────────── Supabase project (Middle East region) ───────────────────────┐   RevenueCat
│  API gateway (PostgREST + RPC)   Auth (Apple, Google, email, JWT)   Storage (avatars)  │◄──webhook──┘
│                                                                                        │
│  PostgreSQL (source of truth)                                                          │
│   ├─ Row-Level Security on every table                                                 │
│   ├─ RPC: submit_prediction() ← lock enforced with DB now() (server clock)             │
│   ├─ append-only: prediction_revisions, points_ledger, audit_log                       │
│   ├─ leaderboard aggregate tables (incremental)                                        │
│   └─ pg_cron schedules → Edge Functions                                                │
│                                                                                        │
│  Edge Functions (TypeScript/Deno)                                                      │
│   ├─ ingest-fixtures (every 6 h) / ingest-live (every 60 s during match windows)       │
│   ├─ settle-match (triggered by MatchFinished / ResultCorrected)                       │
│   ├─ send-notifications (queue drain)                                                  │
│   └─ revenuecat-webhook, admin actions                                                 │
└───────────────┬──────────────────────────────────────────────────────┬─────────────────┘
                │ HTTPS (API key in secrets)                            │
                ▼                                                       ▼
        Football data provider (Part 13)                 Admin web console (Next.js on free static/edge hosting,
                                                         admin role + MFA, all writes audit-logged)
Observability: Sentry/Crashlytics (crashes) · PostHog or Firebase Analytics (events) · Supabase logs + uptime checks + alerts
```

## 11.4 Component decisions

| Concern | Choice (MVP) | Why | Upgrade path |
|---|---|---|---|
| API gateway | Supabase PostgREST + Postgres RPC functions | No server to run. RLS gives per-row security | Put a dedicated API service in front (option B) |
| Auth | Supabase Auth: Sign in with Apple, Google, email magic link/password; MFA (TOTP) for admins | Built-in and free up to 50k MAU (Free) / 100k MAU (Pro) **[verify]** | Keep, or migrate the JWT issuer |
| Database | PostgreSQL (Supabase) | Relational integrity, transactions, constraints, window functions for ranks | Bigger compute, read replicas, partitioning |
| Cache | Postgres aggregate tables + HTTP caching headers + an in-app cache | Redis isn't needed below ~50k WPU; one less service and bill | **Redis sorted sets** (e.g. Upstash, then ElastiCache/Memorystore) for leaderboards at scale |
| Football API integration | Edge Functions on pg_cron schedules | Cheap, close to the DB | A dedicated worker service with a queue |
| Prediction engine | `submit_prediction` RPC in SQL (lock check + revision insert in one transaction) | Atomic, fast, and can't be bypassed | Same |
| Scoring engine | TypeScript pure function `score()` (shared package, exhaustively tested) run by `settle-match` | Deterministic and testable | Same code in a worker |
| Leaderboard engine | Incremental updates to `leaderboard_entries` from the points ledger; ranks by SQL window functions, cached per page | Simple and correct at small scale | Redis sorted sets (`ZINCRBY`/`ZREVRANK`) fed by the same ledger |
| Subscriptions | RevenueCat → webhook → our `subscriptions` + `entitlements` tables | Saves weeks (Part 9) | Native store server APIs |
| Notifications | FCM (free) for both platforms, with an outbox table drained by an Edge Function | Free, reliable, and batched per user caps | Dedicated notification service |
| Analytics | PostHog (free tier) **or** Firebase Analytics, decided in Part 16 | Free at our scale | Warehouse (BigQuery) later |
| Crash reporting | Firebase Crashlytics (free) | Free, standard | – |
| Admin portal | Next.js app using an admin-only role and RPCs (never raw table edits) | Every admin write goes through audited functions | – |
| Monitoring / logging | Supabase logs + a free uptime monitor + alert on settlement lag > 10 min | Enough for MVP | Grafana/Datadog at scale |
| Secrets | Supabase secrets (Edge Functions), CI secrets, **never in the app** | The provider API key must never ship in the mobile app | Cloud KMS / Vault |
| CI/CD | GitHub Actions: tests, lint, DB migrations; Codemagic or GitHub Actions for mobile builds (free tiers) | Free at our volume | – |

## 11.5 Server-authoritative time (the core integrity mechanism)

- `submit_prediction(match_id, home, away, idempotency_key)` runs **inside Postgres**:
  1. `SELECT lock_at, status FROM fixtures WHERE id = $1 FOR SHARE` (locks the row, so a concurrent kick-off change can't race it).
  2. Reject if `now() >= lock_at` or the status isn't open. Postgres `now()` is the only clock used.
  3. Upsert `predictions` (the current value) and insert into `prediction_revisions` (append-only), storing the server timestamp.
  4. Return the saved prediction + `server_time`.
- Every API response carries `server_time`. The app computes `offset = server_time − device_time` and shows countdowns using that offset, so a wrong phone clock can't mislead the user.
- Database triggers **block** any UPDATE of a prediction after `lock_at`, even from admin roles; admin corrections go through a separate audited function. `prediction_revisions`, `points_ledger` and `audit_log` have UPDATE/DELETE revoked for all roles.

## 11.6 Hosting region & data residency (⚠️ legal input needed)

- **Latency:** users are in KSA/UAE, so the region should be in the Gulf. Supabase is reported to offer UAE and Bahrain regions ([Supabase regions](https://supabase.com/docs/guides/platform/regions), via search summary **[verify]**).
- **Saudi PDPL:** transfers of personal data **outside Saudi Arabia** are restricted and need safeguards (e.g. standard contractual clauses) and documentation, since SDAIA has not published an adequacy list ([King & Spalding](https://www.kslaw.com/news-and-insights/international-personal-data-transfers-under-saudi-arabias-data-protection-law), [HFW](https://www.hfw.com/insights/cross-border-data-transfers-in-ksa-standard-contractual-clauses-vs-binding-common-rules/)). Hosting in the UAE or Bahrain is therefore a cross-border transfer for Saudi users.
- **In-Kingdom options:**
  - Google Cloud's Dammam region is only available to KSA customers via CNTXT ([Google Cloud](https://docs.cloud.google.com/docs/dammam-region-access)).
  - AWS has announced a Saudi region by **December 2026** ([About Amazon](https://www.aboutamazon.com/news/aws/aws-cloud-region-saudi-arabia)). If Supabase adds it, migration would be straightforward. **[verify]**

**Recommendation:**
1. **Minimise personal data.** Collect only email/sign-in ID, username, country, date of birth (or just an "over 16" flag), favourite clubs and device tokens. Collect no phone numbers or real names.
2. **Ask counsel (D-017):** is a Gulf region (UAE/Bahrain) acceptable with SCCs and PDPL transfer documentation for the MVP, or is in-KSA hosting required from day one?
3. **Keep the architecture portable** (standard Postgres and portable TypeScript), so that moving to an in-Kingdom region is a migration, not a rewrite.

This is **Q-16**.

## 11.7 Scalability path (feeds Part 27)

| Stage | WPU | Architecture changes | Est. infra / month **[assumption]** |
|---|---|---|---|
| Validate | < 1k | Supabase Free (note: free projects pause after a week of inactivity, so the beta should use Pro) | $0 |
| Launch | 1k–10k | Supabase Pro, default compute | ~$25–50 |
| Growth | 10k–100k | Larger compute add-on, connection pooling tuned, Redis for leaderboards, CDN-cached fixture JSON | ~$100–400 |
| Scale | 100k–1M+ | Read replicas, a dedicated worker service + queue (option B), a partitioned `prediction_revisions` table, a regional CDN, possibly a self-hosted/enterprise Postgres | $1k+ (sized from real load) |

### Lock-time spike (e.g. Al Hilal vs Al Nassr, or El Clásico)
- **Writes are tiny:** one prediction is a single-row upsert + one insert (< 1 KB). Even 2,000 submissions per second is well within a single tuned Postgres at the growth tier. Pooling (Supavisor/PgBouncer) keeps connections bounded.
- **Reads dominate.** Fixture lists and match details come from the in-app cache and CDN-cacheable endpoints (`Cache-Control: max-age=30`). Leaderboards are served from precomputed pages.
- **Smoothing demand:** reminder pushes are spread over ~2 h before lock (not all at T−10 min), and bulk "predict the whole matchweek" encourages early submission.
- **Graceful degradation:** if the DB is under pressure, non-essential features (community distribution, live card) switch off via feature flags. `submit_prediction` is always prioritised.
- **The client handles a close-to-lock submit honestly:** it shows "Sending…" and then a definite ✅/❌ based on the server's answer, never an optimistic ✅.
- **Load test before launch:** simulate 10× the expected peak in the last 10 minutes before a derby kick-off (Part 26).

## 11.8 Mobile app architecture (Flutter)

| Layer | Choice |
|---|---|
| State management | Riverpod |
| Navigation / deep links | go_router + app links / universal links. Deferred deep links for invites (Branch free tier or a custom landing page + store referrer) **[decide in build]** |
| Data | Repositories over `supabase_flutter`, models generated with `freezed`/`json_serializable` |
| Local cache | Drift or Hive for fixtures and boards (offline reading) |
| Localisation | Flutter `gen-l10n` ARB files (ar, en), `intl` for numbers/dates, RTL by default in `ar` |
| Push | `firebase_messaging` + local notifications |
| Billing | `purchases_flutter` (RevenueCat) |
| Quality | `flutter_test`, golden tests in **both LTR and RTL**, `integration_test` for the key flows |
| Security | Certificate pinning **not** used for MVP (too risky to rotate). App Attest / Play Integrity tokens are sent with sign-up and prediction calls (verified server-side, Part 14) |

## 11.9 Environments & delivery
- **Environments:** `dev` (Supabase free, with provider fixtures replayed from recorded JSON), `staging` (a copy of prod settings, test store accounts), `prod`.
- **Database migrations** are version-controlled (Supabase CLI). No manual schema edits in prod.
- **Recorded provider payloads** (real match days, including postponements and corrections) are replayed in CI to test ingestion and settlement end to end.
- **Mobile releases** use staged rollouts on both stores, plus remote config/feature flags for risky features.

## 11.10 Decisions (added to the log)
- **D-025:** Flutter for mobile.
- **D-026:** Supabase (Postgres) + TypeScript Edge Functions for MVP. Postgres is the permanent source of truth.
- **D-027:** Server-authoritative lock is implemented in a Postgres RPC plus triggers.
- **D-028:** No Redis in MVP. Introduce it at ~50k WPU or when leaderboard p95 > 200 ms.
- **D-029:** Hosting region: a Gulf region, pending counsel on PDPL (**Q-16**).

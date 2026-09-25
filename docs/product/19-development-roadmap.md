# PART 19 — Development Roadmap

> Status: Draft v0.1 · 2026-09-25
> Constraints: no fixed launch date, no team, very small budget (D-012, D-016). Build route: **AI-assisted development (Claude) + the founder + part-time freelancers where needed** (see Part 20).
> Calendar anchors: the 2026/27 season is under way; the **AFC Asian Cup 2027 in KSA** (Jan–Feb 2027 **[verify dates]**) is a marketing moment; the **2027/28 season start (Aug 2027)** is the natural public launch. Dates below are **targets, not commitments**.

---

## 19.1 Timeline at a glance (lean path)

```
2026  Oct  Nov  Dec | 2027 Jan  Feb  Mar  Apr  May  Jun  Jul  Aug  Sep
Ph0  ████████                                    Validation: test league, name vote, V5 data trial, counsel opinions
Ph1-3     ███████                                Requirements sign-off, UX/UI design, architecture spikes
Ph4-7          ███████████████                   Backend + mobile + data + prediction engine (core loop)
Ph8-10                   █████████               Gamification basics, subscriptions, admin console
Ph11                           ██████            QA, security, load test, pen test
Ph12                               ██████        Closed beta (test-league players = Play's 12+ testers)
Ph13                                      ███    Store submission → public launch for 2027/28 season
Ph14                                         ███►Analytics, optimisation, V1.1 (Fan Store, challenges, friends)
Asian Cup 2027 (KSA): run a special test-league edition in Jan–Feb → marketing + more validation data
```

Faster is possible with a larger budget (Part 20, "Professional" scenario ≈ 4–5 months of build).

## 19.2 Phases

### Phase 0 — Product validation (now → ~Nov 2026, ~6–8 weeks)
- **Objectives:** prove the weekly habit, the viral groups and club-vs-club interest; choose the name; de-risk data and legal.
- **Deliverables:**
  - 4-week test league results (Tracking tab)
  - name vote winner
  - week-3 survey incl. price test
  - V5 data-provider trial report
  - counsel answers to Q-16/17/19/20
  - trademark search on the winning name
- **Dependencies:** founder's communities; counsel; API-Football free/trial tier.
- **Risks:** low participation; counsel delays; SPL/UAE data not good enough.
- **Definition of Done:**
  - Go/no-go decision recorded with evidence (week-4 retention ≥ 40% target, Part 2 §2.4)
  - name chosen and trademark-searched
  - data provider chosen
  - written legal opinion received

### Phase 1 — Requirements
- **Objectives:** freeze the MVP scope.
- **Deliverables:** approved PRD (Part 22), prioritised backlog, updated decision log.
- **Dependencies:** Phase 0 learnings.
- **Risks:** scope creep.
- **DoD:** founder sign-off; every MVP story has acceptance criteria.

### Phase 2 — UX/UI design
- **Objectives:** a usable, beautiful Arabic-first design.
- **Deliverables:** brand identity (logo, icon, colours, type, Part 18), a design system, and high-fidelity screens for all MVP screens (Part 10) in AR (RTL) + EN, plus a clickable prototype.
- **Dependencies:** final name; the Phase 1 scope.
- **Risks:** designer cost; RTL mistakes.
- **DoD:** 5 test-league players complete "first prediction" and "create group" on the prototype in the target time, unaided.

### Phase 3 — Architecture
- **Objectives:** confirm the technical choices with spikes.
- **Deliverables:**
  - repo (**KooraCall**), CI pipeline
  - Supabase project (dev/staging)
  - a spike proving the `submit_prediction` lock + trigger (Part 11 §11.5)
  - a spike of the provider adapter using recorded payloads
  - the hosting region decision (after Q-16)
- **Dependencies:** repo created by the founder (Q-22); counsel answer Q-16.
- **Risks:** hosting-region constraint.
- **DoD:** spikes pass automated tests; ADRs recorded.

### Phase 4 — Backend foundation
- **Objectives:** schema, security and core RPCs.
- **Deliverables:**
  - DB migrations (Part 12)
  - RLS policies + tests
  - auth (Apple/Google/email)
  - profiles, preferences
  - entitlements
  - rate limiting
  - audit log
- **Dependencies:** Phase 3.
- **Risks:** RLS mistakes.
- **DoD:** RLS test suite green; every table covered; no direct writes possible.

### Phase 5 — Mobile development
- **Objectives:** the Flutter app shell + all MVP screens.
- **Deliverables:**
  - onboarding
  - home
  - matches
  - prediction (single + bulk)
  - leaderboards
  - groups + invite deep links
  - profile
  - settings
  - notifications
  - AR/EN with RTL golden tests
- **Dependencies:** designs (Ph2), APIs (Ph4).
- **Risks:** deep-link/deferred-link complexity; RTL bugs.
- **DoD:**
  - all MVP stories pass acceptance tests on iOS + Android
  - RTL + LTR golden tests green
  - crash-free ≥ 99.5% in internal testing

### Phase 6 — Football API integration
- **Objectives:** reliable fixtures and results for the 8 competitions.
- **Deliverables:**
  - provider adapter
  - ingestion jobs (fixtures, live, results, standings, form/H2H)
  - status mapping (Part 13 §13.5)
  - Arabic club names for ~150 clubs
  - reconciliation job
- **Dependencies:** a paid provider plan (~$19/month).
- **Risks:** provider data gaps for SPL/UAE.
- **DoD:** 2 real matchweeks ingested with zero wrong results and FT reported ≤ 5 min p95.

### Phase 7 — Prediction engine
- **Objectives:** lock, settlement, scoring and leaderboards.
- **Deliverables:**
  - the `score()` function with exhaustive tests
  - settlement pipeline with versioning and corrections
  - leaderboards incl. club-vs-club (D-020)
  - result push
- **Dependencies:** Ph4, Ph6.
- **Risks:** edge cases (abandoned or corrected matches).
- **DoD:**
  - every Part 7 §7.4 status scenario is tested with recorded payloads
  - the settlement SLA of ≤ 15 min p95 is met in staging
  - a leaderboard rebuild from the ledger equals the incremental state

### Phase 8 — Gamification (basic)
- **Objectives:** XP, levels, streaks, 10 MVP badges, and the Coins ledger (store in V1.1).
- **Deliverables:** gamification service + profile UI.
- **Dependencies:** Ph7.
- **Risks:** tuning.
- **DoD:** badges unlock correctly in replay tests; the Coins ledger reconciles with points.

### Phase 9 — Subscriptions
- **Objectives:** Free / Plus / Pro live in both stores.
- **Deliverables:**
  - store products
  - RevenueCat integration + webhook
  - entitlements
  - paywalls (contextual)
  - restore purchases
  - required disclosures
  - Apple Small Business Program enrolment
- **Dependencies:** developer accounts (D-U-N-S number if registering as a company **[verify]**); company registration (Q-19); bank/tax details in the stores.
- **Risks:** store review rejections.
- **DoD:** sandbox purchase, renewal, cancel, refund and restore all tested on both platforms.

### Phase 10 — Admin dashboard
- **Objectives:** operate match days safely.
- **Deliverables:** the Part 15 MVP modules (match ops, users, moderation, flags, audit, admin management).
- **Dependencies:** Ph4, Ph7.
- **Risks:** admin security.
- **DoD:**
  - an abandoned-match drill and a result-correction drill both completed in staging
  - MFA enforced
  - every write audited

### Phase 11 — QA & security testing
- **Objectives:** production quality.
- **Deliverables:**
  - the full test plan (Part 26)
  - load test at 10× the derby peak
  - OWASP MASVS/API self-review
  - an **external pen test** (API + RLS + admin)
  - accessibility check
- **Dependencies:** feature-complete build.
- **Risks:** pen-test findings.
- **DoD:** no open critical/high findings; load-test targets met.

### Phase 12 — Beta
- **Objectives:** real-world validation.
- **Deliverables:**
  - TestFlight + a Google Play **closed test** with test-league players. New personal Play accounts need **≥ 12 testers for 14 days** before production ([IconikAI](https://www.iconikai.com/blog/google-play-developer-account-fee-2026)) **[verify current rule]**.
  - a 3–4 matchweek beta
  - a feedback loop
- **Dependencies:** Ph11.
- **Risks:** low tester engagement.
- **DoD:**
  - Part 5 §5.5 criteria trending to target
  - no settlement errors
  - crash-free ≥ 99.5%

### Phase 13 — App Store / Google Play launch
- **Objectives:** public launch (target: 2027/28 season start).
- **Deliverables:**
  - store listings in AR/EN (ASO keywords, with no gambling words)
  - screenshots
  - privacy labels / data-safety form
  - review notes explaining "free, no prizes, no gambling"
  - the launch campaign with licensed influencers (Mawthooq / UAE permit)
  - a support inbox
- **Dependencies:** legal documents (Part 17 §17.8), trademark filed.
- **Risks:** review rejection; launch-day load.
- **DoD:** approved in both stores, staged rollout to 100%, monitoring green for 7 days.

### Phase 14 — Analytics & optimisation
- **Objectives:** grow WPU and retention, then V1.1.
- **Deliverables:**
  - weekly metrics review (Part 16)
  - A/B tests (paywall, onboarding, reminders)
  - V1.1: Fan Store, challenges, friends + head-to-head, full achievements
- **Dependencies:** launch data.
- **Risks:** seasonality (June–July).
- **DoD:** the 8-week post-launch review against the Part 5 §5.5 criteria is recorded in the decision log.

## 19.3 Critical path
Counsel opinion (Q-20, Q-16) → hosting region + company registration → developer accounts + store products → beta → launch.
**Start the legal and company steps in Phase 0.** They take the longest and can't be sped up with code.

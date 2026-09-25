# PART 26 — Testing Strategy

> Status: Draft v0.1 · 2026-09-25 (referenced as "Part 26" throughout the specification)
> **Priority:** prediction integrity and scoring get the deepest automated testing. A wrong point costs more trust than any UI bug.

---

## 26.1 Test pyramid & tools

| Level | What | Tools | Runs |
|---|---|---|---|
| **Unit** | `score()`, lock rules, status mapping, entitlement checks, streak/XP/badge rules, Flutter view-models | Vitest/Deno test (TypeScript), `flutter_test` | Every commit |
| **Database** | RPCs, triggers, RLS policies, append-only guarantees, constraints | pgTAP (or SQL tests via the Supabase CLI) against a local Postgres | Every commit |
| **Integration** | Ingestion → settlement → leaderboard → notification outbox, using **recorded provider payloads** | TypeScript test harness + local Supabase | Every commit |
| **API** | RPC contracts, error codes, idempotency, rate limits, auth | Contract tests (TypeScript), OpenAPI-style schema checks | Every commit |
| **UI** | Widget tests, **golden tests in AR (RTL) and EN (LTR)**, dynamic type | `flutter_test` goldens | Every commit |
| **End-to-end** | The key journeys on real builds | Flutter `integration_test` (+ Maestro or Patrol for device flows) on emulators; manual device matrix before release | Nightly + pre-release |
| **Performance / load** | The lock-time spike, leaderboard reads | k6 (scripts calling the RPCs) against staging | Pre-beta, pre-launch, before big derbies |
| **Security** | RLS tests, dependency/secret scans, OWASP MASVS + API Top 10 review, external pen test | CI scanners + external vendor | CI + pre-launch |
| **Subscriptions** | Purchase, renew, cancel, refund, restore, grace, upgrade/downgrade | StoreKit testing in Xcode, Play license testers, RevenueCat sandbox | Pre-release |
| **Accessibility** | Screen reader labels, contrast, touch targets | Flutter semantics tests + manual VoiceOver/TalkBack pass | Pre-release |

## 26.2 Scoring & settlement tests (mandatory)
1. **Exhaustive `score()` test:** every combination of predicted and actual scores 0–9 × 0–9 × 0–9 × 0–9 (10,000 cases), compared with an independently written reference implementation.
2. **Property tests:**
   - points ∈ {0, 2, 3, 5}
   - exact ⇒ 5
   - wrong outcome ⇒ 0
   - swapping home/away on both sides keeps the points the same
3. **Status state machine:** every transition in Part 7 §7.4, including illegal ones (FT → live) → HOLD.
4. **Replays of recorded real match days:** a normal day, postponement before kick-off, a same-day kick-off change, AET, PEN, abandoned → resumed / replay / result stands / awarded, and a score correction after 30 min and after 71 h / 73 h (the window edge).
5. **Idempotency:** the same FT event delivered 5× → one settlement. Out-of-order events → the correct final state.
6. **Correction deltas:** v1 → v2 changes points correctly for every outcome pair; leaderboards and Coins adjust; notifications only go to affected users.
7. **Rebuild check:** rebuilding leaderboards from `points_ledger` equals the incremental state (run nightly in staging too).

## 26.3 Required edge-case tests (from the brief)

| Scenario | Expected behaviour | Test type |
|---|---|---|
| Match postponed after predictions | Predictions kept, reopened with the new lock; void after 21 days without a date | Integration (replay) |
| Match abandoned | HOLD → admin decision → correct settlement per option | Integration + admin E2E |
| Score corrected by provider | New version, deltas, notifications, audit | Integration |
| API temporarily unavailable | Retries with backoff, "results delayed" banner, settlement when data returns, no data loss | Integration (fault injection) |
| Duplicate API events | Single settlement (unique keys) | Integration |
| Submit **exactly at cutoff** | `lock_at − 1 ms` accepted; `lock_at` and later rejected | DB test with a controlled clock |
| **Device clock manipulated** | No effect on acceptance; the countdown uses the server offset | Unit (client) + E2E |
| Internet disconnects during submit | Client retries with the same idempotency key; never shows "saved" without confirmation; a retry after lock → clear rejection | E2E with network conditioning |
| Subscription expires | Entitlements revoked at period end/grace; data kept; exclusive cosmetics unequipped, not deleted | Subscription sandbox |
| Leaderboard ties | Shared ranks with the tie-break order points → exacts → results | Unit + DB |
| Concurrent kick-off change during a submit | Row lock (`FOR SHARE`) prevents a race; the result is consistent | DB concurrency test |
| Primary club change twice in a season | Second change rejected | DB test |
| Paid tier tries to affect points | Automated invariant test fails the build | Unit/DB |

## 26.4 Load test targets (pre-launch)
- **Scenario "Derby lock":** 10× the expected peak submit rate for the final 10 minutes before kick-off, plus normal read traffic.
- **Pass criteria:**
  - submit p95 < 300 ms, zero errors other than intended `PREDICTION_LOCKED`
  - leaderboard p95 < 200 ms
  - DB CPU < 70%
- **Scenario "Final whistle":** 20 matches finishing within 10 minutes → all settled within the SLA.

## 26.5 Release gates
A build ships only if:
- all automated suites are green
- the RTL/LTR golden tests pass
- there are no open critical/high security findings
- the subscription sandbox checklist passes
- the manual device matrix passes: two recent iPhones + one older, two Android flagships + one budget Android, in AR and EN

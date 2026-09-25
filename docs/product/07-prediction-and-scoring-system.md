# PART 7 — Prediction & Scoring System

> Status: Draft v0.1 · 2026-09-24
> This is the heart of the product. Every rule here must be **deterministic, server-authoritative, versioned
> and explainable to the user in one sentence.**

---

## 7.1 Core principles

1. **The server clock is the only clock.** Device time is never used for locking or ordering.
2. **Predictions are append-only.** An edit creates a new revision. Nothing is overwritten or deleted.
3. **Settlement is a pure function**: `score(prediction, official_result, ruleset_version) → points`. Re-running it produces the same output.
4. **Every settlement is versioned.** Corrections create a new version, and the user's points are adjusted by the difference.
5. **When in doubt, void.** Where a sporting result is unclear or not the product of play, the prediction is voided rather than guessed.
6. **Paid tiers never affect scoring** (D-002).

## 7.2 Prediction lifecycle

```
            create/edit (before lock_at)
   ┌──────────┐  ───────────────►  ┌──────────┐
   │  (none)  │                    │   OPEN   │◄─────────────┐ match postponed before kick-off
   └──────────┘                    └────┬─────┘              │ and rescheduled (lock_at moves)
                                        │ server time ≥ lock_at
                                        ▼                    │
                                   ┌──────────┐──────────────┘
                                   │  LOCKED  │
                                   └────┬─────┘
             ┌──────────────────────────┼──────────────────────────┐
             ▼                          ▼                          ▼
      ┌────────────┐            ┌─────────────┐            ┌─────────────┐
      │  SETTLED   │──correct──►│ SETTLED v2+ │            │    VOID     │
      │ (points)   │            │ (re-scored) │            │ (0 pts, not │
      └────────────┘            └─────────────┘            │  counted)   │
                                                           └─────────────┘
```
Reopening from LOCKED to OPEN is allowed **only** if the match never kicked off (e.g. postponed shortly before kick-off after the lock had passed). The audit log records it.

## 7.3 Lock rules

| Rule | Definition |
|---|---|
| Default lock | `lock_at = scheduled_kickoff − lock_offset`. `lock_offset` defaults to **0 minutes** (lock at kick-off) and is configurable per competition. Recommendation: start at 0 for simplicity, and raise it to 5 min if provider kick-off times prove unreliable |
| Acceptance | A submit is accepted if and only if `server_received_at < lock_at`, **strictly**. A request arriving at exactly `lock_at` is rejected |
| Kick-off moved **later** (before lock) | `lock_at` moves later. Users are notified if they have no prediction yet |
| Kick-off moved **earlier** | `lock_at` moves earlier. A reminder is sent immediately to users without a prediction |
| Delayed kick-off announced **after** lock | Stay LOCKED (no reopening for short delays) |
| Offline / flaky network | The client sends an idempotency key. If a retry arrives after the lock, it is rejected and the app shows "not submitted: arrived after lock at 20:30:00" |
| Device clock manipulation | No effect. The countdown in the app is computed from the server time offset delivered with each API response |

## 7.4 Match status handling

Settlement uses the **regulation result: 90 minutes + stoppage time**. Extra time and penalty shootouts are ignored for scoring (Q-14).
*Rationale:* this is the standard convention, the same for league and knockout matches, and it avoids the question "does 1–1 (4–3 pens) count as a draw?".
The UI shows it plainly: "Scored on the 90-minute result". The data provider must expose the regulation-time score separately (Part 13 diligence item).

| Provider status | Our state | Prediction outcome | Counts toward accuracy / streaks? |
|---|---|---|---|
| Scheduled / Time TBD | OPEN | editable | – |
| Live (1H, HT, 2H) | LOCKED | "potential points" shown live | – |
| Finished (FT) | SETTLED | scored on the 90-min score | Yes |
| Finished after extra time (AET) / penalties (PEN) | SETTLED | scored on the **90-min** score | Yes |
| Postponed **before** kick-off, new date known | OPEN (reopened), lock moves | prediction kept, editable until the new lock | – |
| Postponed, no new date within **21 days** or by season end | VOID | 0 pts | **No** (neither breaks nor extends a streak) |
| Cancelled | VOID | 0 pts | No |
| Abandoned, later **resumed** from the point of abandonment | LOCKED → SETTLED | scored on the final 90-min result | Yes |
| Abandoned, **replayed** from scratch | LOCKED → SETTLED on the replay | predictions were locked before any play, so they stand | Yes |
| Abandoned, **result awarded** by the authority (forfeit, e.g. 3–0) | VOID | 0 pts | No |
| Abandoned, result stands as at abandonment (authority decision) | SETTLED on the declared result | scored | Yes |
| Suspended / interrupted (temporary) | LOCKED | wait | – |
| Unknown / inconsistent data | HOLD | alert the admin, no settlement | – |

The 21-day window is a proposal. It should be long enough for typical re-arrangements, and short enough that stale predictions don't linger.

## 7.5 MVP scoring model (simple, explainable)

| Outcome | Points | Example (actual 2–1) |
|---|---:|---|
| **Exact score** | **5** | predicted 2–1 |
| **Correct result + correct goal difference** (not exact) | **3** | predicted 3–2 or 1–0 |
| **Correct result only** (win/draw/loss) | **2** | predicted 3–0 |
| Wrong result | 0 | predicted 1–1 or 0–2 |

Draw example (actual 1–1): 1–1 = 5; 0–0 or 2–2 = 3 (a draw always has goal difference 0); 3–1 = 0.

**Why this model**
- Three tiers explain themselves in one sentence each, and the ranking holds: exact beats close beats right-result beats wrong.
- It rewards "reading the game" (goal difference) without the complexity of bonus categories.
- It is in line with established games (Super 6 uses 5/2, Superbru's maximum is 3), so users who come from those apps recognise it.
- A 5-point exact score is rare but reachable, which makes a satisfying "moment".

**Deliberately not in MVP scoring:** BTTS, clean sheets, total goals, upset bonuses, multipliers. They add explanation cost with little benefit at launch.

## 7.6 Advanced scoring model (V2 candidate, data-driven)

Introduce **only** if MVP data shows score clustering (many ties) or a strategy that dominates, such as always predicting 1–0 for the favourite.

| Mechanic | Idea | Guardrail |
|---|---|---|
| **Upset bonus** | +1 or +2 when the user correctly predicts an outcome that < X% of the community predicted | Computed from community picks **at lock** (not odds), so there is no betting-odds dependency |
| **Banker** (confidence pick) | One match per matchweek, points ×2 | Available to all tiers. Must be chosen before that match locks |
| **Goal-count bonus** | +1 for the exact total goals when the result was wrong | Keeps wrong predictions interesting |
| **Weekly perfect bonus** | Achievement only (XP), not points | Keeps points simple |

Each ruleset has an ID and version. A season is always scored under one ruleset. Changes apply from the next season (or the next matchweek for new competitions), and **never retroactively**.

## 7.7 Settlement pipeline

```
Provider result (poll or webhook)
  → Ingestion: normalise + compute result_hash(match_id, status, reg_home, reg_away)
  → if hash unchanged: drop (duplicate)
  → debounce 2 min after first FT (lets providers publish quick fixes)
  → sanity checks: status transition valid? score non-negative? no regression from FT to live?
      → fail → HOLD + admin alert
  → Settlement job (idempotent by match_id + result_version):
        for each locked prediction: points = score(pred, result, ruleset)
        write prediction_result(version = n) in one DB transaction (batch)
        emit PredictionSettled(user, match, points, delta)
  → Leaderboards apply the deltas; gamification evaluates; notifications queued
```
**SLA budget (15 min p95):** provider latency (target ≤ 5 min, to be verified in the V5 trial) + 2 min debounce + settlement and leaderboard update (target < 2 min) + push delivery.

### Corrections
- The provider changes the result (e.g. a goal re-attributed or a data error) → new `result_version` → re-settle → apply point **deltas** to the leaderboards.
- **Correction window:** automatic corrections are accepted for **72 h** after FT. After that, results freeze, and only an admin can re-settle (with a mandatory reason, audit-logged).
- Affected users with a changed score get a push: "Result corrected: HIL 2–2 ITT. Your points changed from 5 to 0."
- Official post-match decisions (e.g. a result overturned by a disciplinary body) are handled as an admin re-settlement.

### Leaderboard ties
Ranking order: total points → number of exact scores → number of correct results → **shared rank** (e.g. two users both shown as "4th"). No arbitrary tie-breakers such as sign-up date.

## 7.8 Anti-exploitation rules

| Exploit | Rule |
|---|---|
| Late submission via clock tricks or replayed requests | Server time + strict `<` lock check + idempotency keys + signed requests (Part 14) |
| Editing after seeing team news/early events | Hard lock at kick-off. Lineups (~1 h before) are fair public information |
| Multiple accounts to "cover" several scores (hedging on groups/boards) | Device attestation (App Attest / Play Integrity), one account per device heuristic, a verified email, and a minimum account age before prizes/featured boards. Flagged accounts are hidden from public boards pending review |
| Accuracy farming (predicting only obvious matches) | The main boards rank on **total points** (volume counts). Accuracy boards need a minimum number of predictions (e.g. ≥ 20 per period) |
| Club-hopping to join the winning fan base | Primary club can change **once per season**, and only future points count for the new club (Part 8) |
| Group scoring abuse | Custom group scoring (Pro) affects **only that group's** board |
| Buying an advantage | No entitlement touches points (§6.4 invariant) |
| Exploiting voids (predicting only when a postponement is likely) | Voids give 0 points and don't count; they give no advantage |
| Insider/admin abuse | Admin overrides need a reason, are audit-logged, and are visible in the admin console; 4-eyes approval for re-settling > N users (V1.1) |

## 7.9 Required test coverage (feeds Part 26)
Property tests for `score()` across all score combinations 0–9 × 0–9 × 0–9 × 0–9. State-machine tests for every status transition in §7.4.
Also covered:
- Exact-cutoff race tests (submit at `lock_at − 1 ms`, `lock_at`, `lock_at + 1 ms`)
- Duplicate and out-of-order event tests
- Correction and re-settlement delta tests
- Leaderboard rebuild equals the incremental state

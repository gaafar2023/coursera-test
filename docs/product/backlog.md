# Prioritised Development Backlog: MVP

> Status: v1.0 draft · 2026-09-25 · Order = build order. P0 = must ship for beta; P1 = must ship for public launch; P2 = should-have.
> Size: S (≤ 2 days), M (3–5 days), L (1–2 weeks) of focused work **[estimates]**. Requirement IDs refer to the PRD (Part 22).

## Epic 0 — Validation & legal (founder-led, Phase 0)
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 0.1 | Run the 4-week test league (kit + spreadsheet ready) | P0 | – | Phase 0 kit |
| 0.2 | Name vote (FootGooal / Mudarraj / KooraCall) → counsel trademark check | P0 | – | D-039 |
| 0.3 | Counsel answers: Q-20 (gambling opinion), Q-16 (hosting), Q-17 (retention), Q-19 (company) | P0 | – | counsel-brief.md |
| 0.4 | V5 data-provider trial (API-Football free tier) on SPL/UAE/UCL | P0 | M | Part 13 §13.3 |
| 0.5 | Create the `KooraCall` repo + grant Claude access | P0 | S | Q-22 |

## Epic 1 — Foundations
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 1.1 | Repo structure (mobile/, backend/, admin/, docs/), CI (lint, tests), branch rules | P0 | S | Part 11 §11.9 |
| 1.2 | Supabase dev/staging projects, migrations tooling, secrets | P0 | S | Part 11 |
| 1.3 | Core schema: reference data, profiles, fixtures, results, predictions, revisions, rulesets, settlements, ledgers | P0 | M | Part 12 |
| 1.4 | RLS policies + RLS test suite; append-only revokes | P0 | M | Part 12 §12.12, Part 14 |
| 1.5 | Auth: Apple, Google, email; 16+ gate | P0 | M | AUTH-1/2 |
| 1.6 | Flutter app shell: routing, theming (dark-first), l10n AR/EN + RTL, golden test setup | P0 | M | L10N-1 |

## Epic 2 — Data ingestion
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 2.1 | Provider adapter interface + API-Football implementation + recorded-payload fixtures | P0 | M | Part 13 §13.5 |
| 2.2 | Fixture sync, standings, form/H2H cache jobs | P0 | M | FIX-1/2 |
| 2.3 | Live + result polling, status mapping, `lock_at` recomputation, HOLD on anomalies | P0 | M | SET-6, PRED-7 |
| 2.4 | Competitions/clubs seed with Arabic names + badge colours (~150 clubs) | P0 | M | D-006 |
| 2.5 | Daily reconciliation vs a second source | P1 | S | Part 13 §13.3 |

## Epic 3 — Prediction core (the heart)
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 3.1 | `submit_prediction_v1` RPC + lock trigger + idempotency + server time | P0 | M | PRED-2/3 |
| 3.2 | `score()` + exhaustive and property tests | P0 | S | SET-1, Part 26 §26.2 |
| 3.3 | Settlement pipeline: debounce, versioning, voids, corrections, deltas | P0 | L | SET-2..5 |
| 3.4 | Leaderboards (all scopes, week/season), ties, snapshots for movement | P0 | L | LB-1/2/4 |
| 3.5 | Club-vs-club computation + primary-club-once rule | P0 | M | LB-3 |
| 3.6 | Bulk prediction RPC | P1 | S | PRED-4 |
| 3.7 | Community distribution (after predicting / lock) | P2 | S | PRED-6 |

## Epic 4 — Mobile MVP screens
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 4.1 | Onboarding incl. first prediction before sign-in | P0 | M | ONB-1..5 |
| 4.2 | Matches list + match details + prediction screen + receipt | P0 | L | FIX, PRED |
| 4.3 | Home (personalised sections) | P0 | M | Part 10 S8 |
| 4.4 | Leaderboards screen (scopes/periods, pinned row) | P0 | M | LB |
| 4.5 | Result breakdown + correction state | P0 | S | US-03 |
| 4.6 | Profile, stats (basic), prediction history | P1 | M | PROF-1 |
| 4.7 | Settings, notification settings, delete account, data export | P0 | M | AUTH-3/4, NOTIF-2 |
| 4.8 | Bulk "predict the matchweek" UI | P1 | S | PRED-4 |
| 4.9 | Live match card | P2 | S | Part 10 S12 |
| 4.10 | Share result card | P2 | S | Part 5 |

## Epic 5 — Groups (growth engine)
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 5.1 | Group RPCs (create/join/leave/remove/regenerate), limits via entitlements | P0 | M | GRP-1..4 |
| 5.2 | Invite links + deferred deep linking + code fallback | P0 | M | GRP-2 |
| 5.3 | Groups UI (list, detail, board, invite share) | P0 | M | Part 10 S15/S16 |
| 5.4 | Pro group tools (custom scoring, co-admins, large groups) | P1 | M | GRP-5 |

## Epic 6 — Notifications & gamification
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 6.1 | Device registration, FCM, outbox + caps + quiet hours | P0 | M | NOTIF-1/2 |
| 6.2 | Reminder, result and weekly-rank pushes + inbox | P0 | M | NOTIF-1/3 |
| 6.3 | XP, levels, streaks (+ freeze), 10 badges | P1 | M | GAM-1..3 |
| 6.4 | Coins ledger (no store UI) | P1 | S | GAM-4 |

## Epic 7 — Subscriptions
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 7.1 | Store products (Plus/Pro × monthly/annual, 7-day trial annual) | P1 | S | SUB-1/3 |
| 7.2 | RevenueCat SDK + webhook → entitlements; restore | P1 | M | SUB-2 |
| 7.3 | Contextual paywalls + disclosures | P1 | M | SUB-3 |
| 7.4 | Plus/Pro features: ad-free flag, advanced stats, full history, cosmetics, advanced notification rules, deep insights | P1 | L | Part 5 §5.6 |
| 7.5 | Invariant test: no entitlement touches points or boards | P0 | S | SUB-5 |

## Epic 8 — Admin console
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 8.1 | Admin app shell, MFA, RBAC | P0 | M | ADM-5 |
| 8.2 | Match-day control, HOLD queue, override + impact preview, re-settle | P0 | L | ADM-1 |
| 8.3 | Competitions/clubs management | P1 | M | ADM-2 |
| 8.4 | Users, evidence view, moderation ladder, reports | P1 | M | ADM-3 |
| 8.5 | Flags, entitlements editor, min app version, audit viewer | P1 | M | ADM-4/5 |

## Epic 9 — Security, analytics, compliance
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 9.1 | Rate limiting, attestation verification, anti-cheat signals | P1 | M | Part 14 §14.5 |
| 9.2 | Weekly anti-cheat detection job + enforcement flags | P1 | M | Part 14 |
| 9.3 | PostHog events per the tracking plan + CI check | P1 | M | Part 16 |
| 9.4 | Username/group-name filters (AR/EN), vocabulary lint on ARB files | P1 | S | Part 17 §17.2/17.7 |
| 9.5 | Legal pages AR/EN in-app, disclaimer, privacy labels, data-safety form | P1 | S | CMP-2/3 |

## Epic 10 — Quality & launch
| # | Item | Pri | Size | Refs |
|---|---|:-:|:-:|---|
| 10.1 | Recorded match-day replay suite in CI | P0 | M | Part 26 §26.2 |
| 10.2 | Load test (derby lock, final whistle) | P1 | M | Part 26 §26.4 |
| 10.3 | External pen test + fixes | P1 | – | Part 20 |
| 10.4 | Closed beta (TestFlight + Play closed test, ≥ 12 testers × 14 days) | P1 | – | Part 19 Ph12 |
| 10.5 | Store listings AR/EN, screenshots, review notes, launch | P1 | M | Part 19 Ph13 |

## After launch — V1.1 (in priority order)
1. Fan Store UI + items (Coins, subscriber-exclusive items) — D-033
2. Friends + head-to-head challenges
3. Weekly/monthly challenges + full achievements
4. Monthly leaderboards
5. Tournament mode (e.g. UCL knockouts)
6. Admin analytics dashboards

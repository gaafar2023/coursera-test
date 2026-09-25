# PART 21 — Risks & Mitigation

> Status: Draft v0.1 · 2026-09-25 · Scale: Likelihood (L) and Impact (I) are rated 1–5. Score = L × I.

| # | Risk | L | I | Score | Mitigation | Owner | Early warning |
|---|---|:-:|:-:|:-:|---|---|---|
| R1 | **Legal/gambling classification** in KSA/UAE | 2 | 5 | 10 | No-Gambling Charter (Part 17), written counsel opinion (Q-20) before beta, vocabulary policy, feature gate | Founder + counsel | Counsel concerns; regulator inquiry |
| R2 | **Weak retention** (no weekly habit) | 3 | 5 | 15 | Phase 0 test league before building, weekly resets, groups, reminders, club-vs-club | Founder | Week-4 retention < 40% in the test league |
| R3 | **Cold start**: empty leaderboards | 3 | 4 | 12 | Seed through the founder's communities/influencers (D-018), private groups as the core, min-fan thresholds shown as "invite more fans" | Founder | Few clubs reach 30 active fans |
| R4 | **Data provider unreliable for SPL/UAE** | 2 | 5 | 10 | V5 trial, adapter layer, Sportmonks fallback, reconciliation job, admin override | Tech | Missing fixtures or late FT in the trial |
| R5 | **Wrong settlement** damages trust | 2 | 5 | 10 | Versioned settlement, 2-min debounce, sanity checks, HOLD queue, correction flow, exhaustive tests | Tech | Support tickets about scores |
| R6 | **Data residency (PDPL)** forces a costly hosting change | 3 | 3 | 9 | Data minimisation, portable stack, counsel answer (Q-16) before build | Founder + counsel | Counsel requires in-KSA hosting |
| R7 | **Name/trademark conflict** (similar apps exist for several candidates) | 3 | 3 | 9 | Community vote + trademark search before design; file TM before launch (Part 18) | Founder + counsel | Search hits / opposition |
| R8 | **IP complaints** (crests, kits, player images) | 2 | 3 | 6 | Generated badges only, disclaimer, no player images (D-006) | Founder | Takedown notice |
| R9 | **App-store rejection** | 2 | 4 | 8 | Review notes, no gambling signals, account deletion, IAP only, privacy labels | Tech | Rejection message |
| R10 | **Cheating / multi-accounts** undermine the boards | 3 | 3 | 9 | Server lock, attestation, hidden-until-lock predictions, detection jobs, enforcement ladder (Part 14) | Tech + ops | Suspicious clusters |
| R11 | **Budget runs out** before launch | 3 | 4 | 12 | Lean scenario, free tiers, phase gates, no spending before Phase 0 passes | Founder | Burn vs plan |
| R12 | **Solo-founder / key-person dependency** | 4 | 3 | 12 | All knowledge in docs + repo, CI, freelancer familiar with the code, runbooks | Founder | Delays |
| R13 | **Seasonality** (June–July drop) | 4 | 2 | 8 | Tournament modes, off-season content, annual plans sold at season start | Product | Summer WPU drop |
| R14 | **Low willingness to pay** | 3 | 3 | 9 | Phase 0 price test, sponsorship/B2B pillars, low running costs (break-even at ~25–50 subscribers, Part 20) | Founder | Conversion < 1% |
| R15 | **Security breach / data leak** | 2 | 5 | 10 | Minimal data, RLS tests, pen test, secrets hygiene, breach runbook | Tech | Anomalous access |
| R16 | **Lock-time traffic spike** outage (derbies) | 2 | 4 | 8 | Load test at 10× peak, pooling, caching, degradation flags | Tech | Latency alerts |
| R17 | **Influencer/advertising non-compliance** (Mawthooq / UAE permit) | 2 | 4 | 8 | Verify licences before contracts; contract template (Part 17) | Founder | – |
| R18 | **Cultural/content missteps** (names, avatars, timing) | 2 | 3 | 6 | Filters, moderation, native review, Ramadan-aware timing | Ops | Complaints |
| R19 | **Competitor copies the club-vs-club idea** | 3 | 2 | 6 | Move fast in the Arabic market, community lock-in, brand | Founder | Competitor launches |
| R20 | **Scope creep** delays MVP | 4 | 3 | 12 | MVP definition (Part 5), decision log, backlog discipline | Founder + Claude | Stories added mid-phase |

**Top risks to act on now (score ≥ 12):**
- **R2 retention:** run the test league.
- **R3 cold start:** line up communities and influencers.
- **R11 budget:** Scenario A + phase gates.
- **R12 key-person:** keep everything documented in the repo.
- **R20 scope creep:** hold the MVP line.

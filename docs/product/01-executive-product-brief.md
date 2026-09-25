# PART 1 — Executive Product Brief

> Working title: **[App Name TBD]** (naming happens in Part 18)
> Status: Draft v0.1 for founder review · Date: 2026-09-24
> Scope: App #1 of 2 (football match prediction). App #2 is out of scope until you share it.

---

## 1.1 One-line summary

A free-to-play, **Arabic-first and English** football prediction game where fans predict scores for the
leagues and clubs they care about, earn virtual points, and compete with friends, their country and,
most importantly, **other clubs' fans**. There is no money wagering of any kind.

## 1.2 The problem, stated honestly

"Predicting football scores" is not a problem on its own. Fans already do it for free in group chats,
on social media and in apps such as Superbru and Sky Super 6. The real jobs a fan wants done are:

| Job to be done | What fans do today | Why that is unsatisfying |
|---|---|---|
| "Prove I know football better than my friends" | Arguing in WhatsApp groups, screenshots of predictions | Nobody keeps score, and results get disputed or forgotten |
| "Represent my club" | Social-media rivalry (e.g. Al Hilal vs Al Nassr, Madrid vs Barça) | Nothing measures it and there is no structured competition |
| "Make every match matter to me, not just my team's" | Fantasy football, betting | Fantasy is complex and time-heavy. Betting is haram for many users in our core market and illegal in several of them |
| "Have a reason to talk football with my group every week" | Office and family sweepstakes by hand | Someone has to administer it manually |

**Our proposition is structured bragging rights**: a trustworthy, automatic scorekeeper for football
opinions, organized around club identity and private groups.

## 1.3 Who it is for (detail in Part 3)

- **Primary launch segment (assumption to validate):** Arabic-speaking football fans aged 18–35 in the
  GCC (Saudi Arabia and the UAE first). They follow both their local league (Saudi Pro League, UAE Pro
  League) and a European club. Smartphone use in Saudi Arabia is among the highest in the world,
  and football dominates sports interest there ([DataReportal Digital 2026: Saudi Arabia](https://datareportal.com/reports/digital-2026-saudi-arabia);
  [Gulf Tech News, June 2026](https://gulftech-news.com/en/2026/06/01/fifa-world-cup-2026-sparks-fan-momentum-in-saudi-arabia-with-9-in-10-of-tiktok-users-following-football/)).
- **Secondary segment:** English-speaking fans worldwide. Superbru and others already serve this crowded
  market, so we do not lead with it.
- **Tertiary / B2B:** companies, universities and fan clubs running private leagues. This is a future
  revenue stream, not an MVP feature.

## 1.4 Core loop

```
Pick match → Predict score (≤10 s) → Kickoff locks prediction (server time)
   → Watch live: "you'd get +5 if it ends like this" → Auto-settlement minutes after full time
   → Points + XP + rank change → Push: "You climbed 24 places / Al Hilal fans now #1"
   → Next matchday is already open → repeat
```

The loop must be **fast**. Users complain publicly that a leading competitor takes 2–3 hours to update
results after a game ([Superbru on Google Play](https://play.google.com/store/apps/details?id=properties.superbru&hl=en), user reviews).
Settlement within ~5–15 minutes of the official final whistle is a product requirement, not a
nice-to-have.

## 1.5 What makes it different (hypotheses, validated in Part 2)

1. **Club-vs-club fan leaderboards as the headline feature**, not a hidden filter. "Which fan base
   predicts best?" turns individual play into tribal, shareable competition.
2. **Arabic-first with real RTL**, and first-class Saudi Pro League, UAE Pro League and AFC coverage.
   We did not find a well-known prediction game positioned this way (see Part 2 for how far we searched).
3. **Speed and trust**: near-instant settlement, a transparent scoring explanation for every match, and an
   immutable prediction record.
4. **Halal-by-design positioning**: no betting, no odds, no gambling ads. This is both an ethical stance
   and a marketing message in the core market.
5. **Respectful monetization**: no interstitial ad walls (a documented competitor complaint), and paid
   tiers never buy leaderboard points.

## 1.6 Business model (summary; detail in Part 9 and Part 20)

- **Freemium subscription** as the primary revenue stream: stats and insights, analytics, more private
  groups, cosmetics, ad-free. Paid tiers **never** affect points.
- **Sponsorship / brand-sponsored challenges** as the second stream. Brands in the GCC spend heavily on
  football. Sponsors get branded challenges with badges and XP only, never prizes (No-Gambling Charter, Part 17).
- **Light, non-intrusive advertising** for free users (native units only, never mid-prediction), and
  never gambling advertising.
- **B2B private leagues** (corporate/university) later.

Reference point: Superbru's premium tier is ~£2/month and offers ad-free, insights, more pools and a
premium badge ([Superbru Premium](https://www.superbru.com/premium)). That is evidence the
cosmetic/convenience model works in this category, and also evidence that consumer willingness to pay is
**low**. We should not build a business plan that depends on high ARPU from consumers alone.

## 1.7 My position on your initial assumptions (challenges)

| Your assumption | My view | Recommendation |
|---|---|---|
| Four tiers (Standard / Gold / Platinum + Free) | Too many for launch. Each extra tier splits a small paying base and adds decision friction at the paywall. | **Founder decision (2026-09-24): Free + two paid tiers** (each monthly + annual). See D-003. |
| Support many leagues at launch | Every league adds data cost, settlement edge cases, QA and content. | **Approved: 8 competitions**: Saudi Pro League, UAE Pro League, EPL, La Liga, Serie A, Bundesliga, Ligue 1, UCL. Expand based on demand. |
| "Pick favorite club" is an onboarding step | Correct, and it is actually the core of our differentiation. | Make it mandatory (at least one), and make club leaderboards visible on the home screen. |
| MVP includes admin + subscriptions + notifications + i18n | Mostly right, but the full subscription matrix is premature. | MVP includes **billing infrastructure + both paid tiers**. The admin portal is minimal (fixtures override, settlement re-run, user moderation). |
| Two apps at once | Splitting a founding budget across two products usually kills both. | Build App #1 to product-market-fit signals first. Share App #2 so we can check for shared infrastructure. |

## 1.8 Critical risks flagged up-front

1. **Legal / gambling classification (HIGH).** Free-to-play with virtual points is the safe design.
   Risk appears the moment we add **real-world prizes**, **paid entry**, or **purchasable points**.
   Apple treats contests/sweepstakes and real-money gaming as special categories (Guideline 5.3) ([Apple App Review Guidelines](https://developer.apple.com/support/downloads/terms/app-review-guidelines/App-Review-Guidelines-English-UK.pdf)).
   Google Play has a dedicated Real-Money Gambling, Games and Contests policy ([Play Console Help](https://support.google.com/googleplay/android-developer/answer/9877032?hl=en)).
   The UAE now has a federal gaming regulator (GCGRA) whose remit includes sports wagering and
   online gaming, and commentators say prize-based competitions are no longer a grey area there
   ([GCGRA](https://www.gcgra.gov.ae/en/); [Global Law Experts](https://globallawexperts.com/esports-events-uae/)).
   Saudi Arabia prohibits gambling. **Founder decision (2026-09-25): the product will never include wagering, paid entry, cash or real-world prizes, or purchasable points or coins.** See the No-Gambling Charter in Part 17.
2. **Data licensing & IP (HIGH).** Club crests, league logos and player images are trademarked and
   licensed separately from match data. Many API plans license **data**, not **logos**.
   MVP should use club names + neutral color badges unless we confirm media rights in the data contract.
3. **Seasonality (MEDIUM).** European leagues pause in June–July. Mitigation: tournaments (the AFC Asian
   Cup 2027 is hosted in Saudi Arabia, per the [Saudi Press Agency](https://www.spa.gov.sa/en/N2549314)), summer friendlies, and off-season challenges.
4. **Cold start (MEDIUM).** Leaderboards and groups are empty at launch. Mitigation: private groups as the
   acquisition wedge (a user invites their existing WhatsApp group), plus club leaderboards that are
   meaningful even at a small scale.
5. **Competition from free big-prize games (MEDIUM).** Sky Super 6 offers £250,000+ jackpots for free
   ([Sky Super 6](https://super6.skysports.com/)). We cannot outbid broadcasters on prizes, so we compete on
   identity, community and local relevance instead.

## 1.9 Recommended scope for MVP (preview; full reasoning in Part 5)

**In:** email/Apple/Google sign-in · league + club onboarding · fixtures · score prediction with a
server-authoritative lock · automatic settlement · simple points (Part 7) · global/club/friends/private-group
leaderboards (weekly + season) · profile with basic stats · push notifications with granular controls ·
EN/AR with RTL · two paid tiers via App Store/Play billing · minimal admin portal · analytics.

**Out of MVP:** head-to-head challenges, comments/social feed, live match events feed, a third paid tier, prizes, B2B leagues, web app.

## 1.10 Tentative technical direction (decided in Part 11)

- **Mobile:** Flutter (one codebase, strong RTL/Arabic text support, consistent rendering). This is tentative and will be compared against React Native in Part 11.
- **Backend:** a modular monolith (TypeScript/NestJS or Python/FastAPI, TBD in Part 11) on PostgreSQL + Redis.
  Leaderboards use Redis sorted sets. Settlement runs as an idempotent worker queue. The server clock is the sole source of truth.
- **Football data:** API-Football and Sportmonks are the leading candidates. The final pick happens after a
  coverage and licensing check for Saudi and UAE leagues (see Part 2.6 and Part 13).

## 1.11 Success metrics for the MVP (targets are assumptions, to be calibrated after the pilot)

| Metric | Why it matters | Initial target (assumption) |
|---|---|---|
| Onboarding → first prediction | Core activation | ≥ 70% of sign-ups predict within the first session |
| Predictions per active user per matchweek | Depth of engagement | ≥ 5 |
| Week-4 retention (in-season) | Habit formation | ≥ 25% |
| % users in ≥1 private group | Network effect / viral loop | ≥ 30% |
| K-factor from group invites | Organic growth | ≥ 0.3 |
| Free → paid conversion | Business viability | 2–4% (to be validated) |
| Settlement latency p95 | Trust | ≤ 15 min after official FT |

---

## Decision Log (running)

| # | Date | Decision | Rationale | Status |
|---|---|---|---|---|
| D-001 | 2026-09-24 → 2026-09-25 | **Permanent (not just MVP):** no wagering, no paid entry, no cash or real-world prizes, no purchasable points or coins, no gambling vocabulary or advertising. Full compliance with KSA, UAE and GCC law (No-Gambling Charter, Part 17) | Founder principle + legal/app-store risk (Apple 5.3, Google RMG policy, UAE GCGRA, KSA gambling prohibition, KSA prize-competition rules) | **Approved (founder), permanent** |
| D-002 | 2026-09-24 | Paid tiers never grant extra leaderboard points or multipliers | Competition integrity is the product's core value | **Approved** |
| D-003 | 2026-09-24 | Launch with **Free + 2 paid tiers** (not 4). The two paid tiers must be clearly differentiated, and neither affects points | Founder decision. It keeps the paywall simpler than 4 tiers while offering a premium option for power users | **Approved (founder)**, revised from my Free + 1 proposal |
| D-004 | 2026-09-24 | GCC / Arabic-first launch market; English supported from day one | Differentiation vs English-centric incumbents | **Approved** |
| D-005 | 2026-09-24 | Launch with 8 competitions: Saudi Pro League, UAE Pro League, EPL, La Liga, Serie A, Bundesliga, Ligue 1, UCL | Data cost + QA scope | **Approved (founder)** |
| D-006 | 2026-09-24 | No official crests, league logos or player photos in MVP. Use our own generated badges (club short code in the club's colours on a generic shape), include a "not affiliated" disclaimer, and license imagery later | Trademark risk. Founder delegated the decision. See Part 5 §5.7 | **Approved (delegated)** |
| D-007 | 2026-09-24 | Focus on App #1 until it shows traction | Budget/focus | **Approved** |
| D-008 | 2026-09-24 | Settlement SLA: points are awarded within 15 min (p95) of the provider reporting full time, and later corrections are handled by versioned re-settlement | Trust. Directly answers competitor complaints | **Approved** |
| D-009 | 2026-09-24 | Private groups (with WhatsApp invite links) and club leaderboards are in the MVP | They are the growth engine and the core differentiator (see Part 5) | Proposed |
| D-010 | 2026-09-24 | North-star metric: Weekly Predicting Users (WPU) | Measures the core habit, not vanity installs (Part 4) | Proposed |
| D-011 | 2026-09-24 | Expansion path after KSA/UAE: other Arab markets (candidates: Qatar, Kuwait, Bahrain, Oman, Egypt, Jordan, Morocco), each adding its local league and needing its own legal check | Founder decision. Arabic-first design carries over directly | **Approved (founder)** |
| D-012 | 2026-09-24 | No fixed launch date and no team yet. Plan for a lean, validation-first path, with the build solo, outsourced, or a small hire decided per phase (Part 19/20) | Founder decision | **Approved (founder)** |
| D-013 | 2026-09-24 | Project moves to its own repository | Project hygiene | **Approved (founder)**, name pending |
| D-014 | 2026-09-24 | Minimum age 16+ until counsel confirms | Compliance | **Approved (founder)** |
| D-015 | 2026-09-24 | Plus/Pro feature split as in Part 5 §5.6 | Paywall clarity | **Approved (founder)** |
| D-016 | 2026-09-24 | **Very small budget → lean build.** Prefer managed services with free tiers, a single low-cost data provider, and no paid tools that aren't essential. Validate with the concierge league before spending | Founder budget constraint | **Approved (founder)** |
| D-017 | 2026-09-24 | Founder's existing KSA/UAE counsel review: T&Cs, privacy policy, age gate, badge/IP approach, and the "no prizes" design | Counsel available | **Approved** |
| D-018 | 2026-09-24 | Cold start via founder's fan-community, influencer and club network (seed the concierge league and the beta) | Founder has these relationships | **Approved** |
| D-019 | 2026-09-25 | Scoring MVP: exact 5 / result + GD 3 / result 2; 90-min result for all matches; void on cancel/award/long postponement | Simple and explainable (Part 7) | Proposed (Q-13, Q-14) |
| D-020 | 2026-09-25 | Club-vs-club = mean points of active fans, min 30 active fans; primary club changeable once per season | Rewards quality, not fan-base size (Part 8) | Proposed (Q-15) |
| D-021 | 2026-09-25 | Game week = Tuesday 04:00 → Tuesday 03:59 Asia/Riyadh | Keeps Monday-night games in the weekend round, starts with the UCL | Proposed |
| D-022 | 2026-09-25 | Community prediction distribution hidden until the user has saved a prediction (or after lock) | Avoids herding bias | Proposed |
| D-023 | 2026-09-25 | Billing through RevenueCat (free tier), with our backend as the entitlement source of truth; enrol in Apple's Small Business Program | Saves weeks for a lean build (Part 9) | Proposed |
| D-024 | 2026-09-25 | 7-day free trial on annual plans only; ads off until ~5k WPU, native-only and never gambling | Retention + UX (Part 9) | Proposed |
| D-025 | 2026-09-25 | Flutter for iOS + Android | Most consistent Arabic RTL, one codebase, lowest cost (Part 11) | Proposed |
| D-026 | 2026-09-25 | Supabase (Postgres, Auth, Edge Functions) + TypeScript for MVP; Postgres is the permanent source of truth | Production-grade integrity at ~$0–25/month (Part 11) | Proposed |
| D-027 | 2026-09-25 | Prediction lock enforced inside Postgres (RPC + triggers, DB clock); revisions/ledger/audit are append-only | Tamper-proof predictions (Parts 11, 12) | Proposed |
| D-028 | 2026-09-25 | No Redis in MVP; add at ~50k WPU or leaderboard p95 > 200 ms | Cost and simplicity | Proposed |
| D-029 | 2026-09-25 | Gulf hosting region, pending counsel on Saudi PDPL cross-border rules; keep the stack portable to an in-KSA region | Legal + latency (Q-16) | **Needs counsel** |
| D-030 | 2026-09-25 | API-Football as the primary data provider (pending V5 trial), Sportmonks as fallback | Cheapest fit for 8 competitions (Part 13) | Proposed |
| D-031 | 2026-09-25 | The provider is never called from the app; one adapter interface; raw payloads kept 90 days | Security, cost, provider independence | Proposed |
| D-032 | 2026-09-25 | All app writes go through versioned, idempotent RPCs that return the server time | Integrity + safe retries | Proposed |
| D-033 | 2026-09-25 | Fan Store with a separate earn-only currency (Coins = earned points 1:1 + bonuses). Cosmetics only; some items need Plus/Pro; no coin multipliers; Coins never sold, cashed out, transferred or used in random rewards. Coins ledger in MVP, store in V1.1 | Founder idea, adjusted so shopping never lowers rank (Part 8 §8.8) | Proposed (Q-18) |

## Open Questions (need your input)

See [`open-questions.md`](./open-questions.md).

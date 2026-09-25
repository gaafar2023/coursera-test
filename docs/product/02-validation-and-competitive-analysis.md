# PART 2 — Product Validation & Competitive Analysis

> Status: Draft v0.1 · Research date: 2026-09-24
> **Research limits (read this first):** Research was done by web search. Several vendor sites
> (e.g. sportmonks.com) blocked direct page fetches from my environment, so some figures come from search-result
> summaries of official pages or from **third-party** comparison blogs. Every such figure is marked
> **[verify]**. Some comparison blogs are published by competing vendors and are therefore biased. I did
> **not** access app-store download counts or revenue estimates (for example from Sensor Tower or data.ai), so I make no
> market-size claims from them.

---

## 2.1 Validation questions — direct answers

### Who is the ideal customer?
An **Arabic-speaking, 18–35-year-old football fan in Saudi Arabia or the UAE** with these traits:
- Strongly identifies with one local club (e.g. Al Hilal, Al Nassr, Al Ittihad, Al Ahli, Al Ain, Al Wahda) **and**
  follows a European giant.
- Already spends time in football group chats and social feeds with friends. In a June 2026 survey
  reported by Gulf Tech News, 88% of Saudi TikTok users follow football
  ([Gulf Tech News](https://gulftech-news.com/en/2026/06/01/fifa-world-cup-2026-sparks-fan-momentum-in-saudi-arabia-with-9-in-10-of-tiktok-users-following-football/)).
- Wants competition and recognition but avoids betting for religious, legal or personal reasons.

*Status: hypothesis. It must be validated by the experiments in 2.4.*

### What problem does this solve beyond predicting scores?
1. **Settles arguments with data.** It keeps a permanent, trustworthy record of who actually knows football.
2. **Gives club identity a scoreboard.** Fan-base-vs-fan-base rankings turn rivalry into a game.
3. **Runs the group competition for you.** Friends, office, university and family leagues run themselves, with
   no spreadsheet.
4. **Makes neutral matches meaningful.** A dull mid-table game matters when points are at stake.

### Why would users return every week?
- **Fixture cadence** creates natural weekly triggers (matchweek opens → deadline → results).
- **Weekly leaderboard resets** give everyone a fresh chance, even after a bad start.
- **Group pressure**: your friends see your rank.
- **Club pride**: "your club's fans dropped to #3, so predict to help."
- **Streaks and achievements**, tuned carefully to avoid burnout (Part 8).

The risk is that people who fall far behind in season rankings stop playing. Mitigation: weekly and monthly
boards, divisions/leagues by skill band (a V1.1 candidate) and personal-best goals.

### What creates network effects?
- **Private groups** (the strongest effect): each group is a mini-network. Its value grows with every friend
  who joins, and leaving means abandoning your group's history.
- **Club leaderboards**: each new fan of a club improves that club's standing, so fans recruit fans.
- **Head-to-head challenges** (post-MVP): these create direct invites.
These are *local* network effects (friend-group level). They are weaker than a true social network but
real, and they are what makes incumbents sticky.

### What makes this different from existing prediction apps?
See 2.3 (the competitor table) and 2.5 (the gaps). In short: **Arabic-first + GCC leagues + club-vs-club
fan competition + fast, transparent settlement + halal-by-design positioning.**

### What could cause the application to fail? (pre-mortem)
| Failure mode | Likelihood | Early warning signal | Mitigation |
|---|---|---|---|
| Users try once and don't come back (no habit) | High | Week-2 retention < 15% | Weekly resets, reminders before cutoff, group invites during onboarding |
| Cold start: empty leaderboards feel dead | High | Few users per club | Launch in a focused geography, seed with fan communities and influencers |
| Can't differentiate from free big-prize games | Medium | Low organic installs, "why not Super 6?" feedback | Community and identity positioning, local leagues |
| Data errors or slow settlement destroy trust | Medium | Support tickets about scores | Multi-source verification, settlement versioning, admin override |
| Legal action or app-store rejection over prizes | Medium (if prizes added) | Rejection citing Guideline 5.3 | No prizes in MVP, counsel before any prize |
| IP complaints over club crests | Medium | Takedown notices | Neutral badges or licensed imagery only |
| Monetization too weak to cover data + infra | Medium | Conversion < 1% | Sponsorship and B2B revenue, keep infra lean |
| Seasonality kills engagement in summer | High (Jun–Jul) | Summer DAU collapse | Tournaments, friendlies, off-season quizzes |

### Which assumptions need validation (ranked by risk × uncertainty)?
1. **A1:** GCC fans will play a prediction game *weekly* when there are no prizes (identity + social is enough).
2. **A2:** Club-vs-club leaderboards are more motivating than global rank.
3. **A3:** Users will create and invite to private groups (the viral loop exists).
4. **A4:** ≥2% of engaged users will pay for non-competitive premium features.
5. **A5:** An affordable data provider has reliable, timely Saudi Pro League and UAE Pro League results with commercial rights for our use case.
6. **A6:** Arabic-first is a meaningful advantage (vs. English apps with Arabic translation).
7. **A7:** Brands will sponsor challenges (the B2B revenue channel).

### What is the strongest potential competitive advantage?
**Community lock-in inside club and friend groups in an under-served language and region.** Features can
be copied. A season of your friends' prediction history plus your club's fan standings cannot be copied quickly.
Being the default app where "Hilal and Nassr fans settle it" is a defensible position if we reach
it first.

### What sounds exciting but should NOT be built initially?
| Tempting feature | Why not now |
|---|---|
| Cash or physical prizes | Legal/app-store classification risk (see 2.7) |
| More than two paid tiers | Conversion friction, small paying base (founder chose Free + 2 paid, D-003) |
| Live in-match predictions ("next goal") | Much higher data cost, latency engineering, and it resembles in-play betting |
| Comments / social feed | Moderation cost, legal exposure, not core to the loop |
| AI "expert predictions" | Can bias users, adds cost, looks like tipster/betting content |
| Player-level predictions (first scorer) | Adds data complexity, and it resembles betting markets |
| 50+ leagues | Data cost + QA |
| Web app | Mobile first, and a web app doubles the front-end work |
| Custom avatars / NFT / crypto | Distraction plus regulatory risk |

### How can we validate demand cheaply before heavy development?
See **2.4 Validation Plan**.

---

## 2.2 Market context (what we could verify)

- **Saudi Arabia:** DataReportal publishes a *Digital 2026: Saudi Arabia* report covering internet, social and
  mobile adoption ([DataReportal](https://datareportal.com/reports/digital-2026-saudi-arabia)). Pull the
  exact figures from the report before using them in investor material. **[verify]**
- **Football interest:** 88% of Saudi TikTok users follow football, per a June 2026 article
  ([Gulf Tech News](https://gulftech-news.com/en/2026/06/01/fifa-world-cup-2026-sparks-fan-momentum-in-saudi-arabia-with-9-in-10-of-tiktok-users-following-football/)).
  This is platform-sourced data, so it may be self-serving.
- **Upcoming anchor event:** Saudi Arabia hosts the **AFC Asian Cup 2027**, and the organizers are piloting an
  official fan-identity app, "Ahlan" ([Saudi Press Agency](https://www.spa.gov.sa/en/N2549314)). This is a natural
  launch or pilot moment. Exact tournament dates are **[verify]**.
- **UAE regulation shift:** the GCGRA (est. 2023) regulates lottery, internet gaming, sports wagering and casinos,
  and Federal Decree-Law No. 25/2025 took effect on 1 June 2026 ([GCGRA](https://www.gcgra.gov.ae/en/);
  [Global Law Experts](https://globallawexperts.com/gcgra-gaming-license-uae-requirements-process-and-what-the-june-2026-civil-code-changes-mean-for-operators/)).
  Legal-commentary source, **[verify with counsel]**.

**Explicitly not claimed:** total market size, number of prediction-app users, or competitor download
and revenue numbers. We do not have reliable sources for these yet. A paid app-intelligence tool
(Sensor Tower / data.ai / AppMagic) would be a worthwhile ~1-month spend in Phase 0.

---

## 2.3 Competitor analysis

### Competitor landscape map

```
                    PRIZE-DRIVEN
                         ▲
       Sky Super 6 ●     │     ● Premier League (official) predictor games
                         │
  SIMPLE ◄───────────────┼───────────────► COMPLEX
  (score picks)          │           (fantasy / squads)
       Superbru ●        │     ● Fantasy Premier League
   Group-chat / manual ● │
        ★ US (target)    │
                         ▼
                 COMMUNITY/IDENTITY-DRIVEN
```

### Detailed comparison

| | **Superbru** | **Sky Super 6** | **Fantasy (e.g. FPL)** | **Score/stat apps (FotMob, OneFootball)** | **Manual (WhatsApp groups)** |
|---|---|---|---|---|---|
| **Core proposition** | Free predictor games across football, rugby, F1, with pools vs friends | Free-to-play: predict 6 scores for a jackpot | Manage a virtual squad | Live scores, stats, news | Friends predicting informally |
| **Prediction mechanic** | Predict every league match. Scoring rewards accuracy, max **3 pts** for an exact score ([Superbru how to play](https://www.superbru.com/premierleague_predictor/how_to_play.php)) | 6 selected fixtures + "Golden Goal" tiebreaker. **2 pts** correct result, **5 pts** exact score ([Squawka](https://www.squawka.com/en/news/sky-bet-super-6/), [Super 6 T&Cs](https://super6.skysports.com/terms)) | Player-performance points | Mostly none. FotMob is described as "passive, you read not play" ([Tiki](https://www.tikitaka.gg/articles/8-best-fotmob-alternatives-for-live-scores-predictions-in-2026), competitor-authored) | Whatever the group agrees |
| **Gamification** | Leaderboards, pools, badges; weekly prizes promoted on some games ([Superbru PL](https://www.superbru.com/premierleague_predictor/land.php?pageId=46)) | Jackpot, weekly points prize, private leagues, "beat the pundits" | Deep: mini-leagues, chips, ranks | Minimal | None |
| **Social** | Private pools (the core strength) | Private leagues | Mini-leagues (the core strength) | Low | High but unstructured |
| **Monetization** | Ads + Premium ~**£2/month**: ad-free, insights, more pools (10→15), premium badge ([Superbru Premium](https://www.superbru.com/premium)) | Marketing funnel for a betting brand (operated under Sky Bet branding, per its Play Store listing ([Google Play](https://play.google.com/store/apps/details?id=com.skybet.app.super6&hl=en_GB))) **[verify ownership]** | Sponsorship / league marketing | Ads + subscriptions | None |
| **UX strengths** | Mature, multi-sport, strong pools | Very simple (6 picks), a huge prize hook | Very deep engagement | Excellent data UI | Zero friction |
| **UX weaknesses / complaints** | Reviews cite intrusive ads (incl. forced ad views), crashes, slow result updates ("2–3 hours") ([Google Play](https://play.google.com/store/apps/details?id=properties.superbru&hl=en), [App Store](https://apps.apple.com/us/app/superbru/id613066215)). The app is being split into sport-specific apps, and some users call the new Rugby app a step back ([Superbru news](https://www.superbru.com/news/the-superbru-app-is-changing-soon)) | Only 6 fixtures (UK-centric), tied to a gambling brand, which is inappropriate for our market | Time-consuming and complex for casual fans | No game | No record-keeping, disputes, admin burden |
| **Arabic / GCC focus** | Not evident **[verify]** | No (UK) | League-specific | Some Arabic support in the scores apps **[verify]** | Yes (organically) |

*Sources for complaints are individual app-store reviews. They show pain points exist but do not measure
how common those pain points are.*

### Other observations
- **Official league apps and fan apps** (e.g. the Premier League and clubs) periodically run predictor games
  with prizes. They have brand power but are single-league and are not built around multi-club rivalry.
- **Tipster / "prediction" websites** for the Saudi Pro League (Forebet, predictZ, etc.) dominate search results for
  "Saudi Pro League predictions". These are **betting-tips** sites, not fan games. The keyword space is
  polluted by betting content, which matters for ASO/SEO and brand positioning.
- In our searches we did **not** find a prominent Arabic-first fan prediction game centered on GCC leagues.
  Absence in web search is weak evidence, so the next step is a manual App Store / Play Store search in the
  KSA and UAE storefronts, in Arabic (e.g. "توقع نتائج المباريات", "توقعات الدوري السعودي"). **[verify — Phase 0 task]**

---

## 2.4 Validation plan (Phase 0, ~6–8 weeks, low cost)

| # | Experiment | Tests | Cost (assumption) | Success threshold (assumption) |
|---|---|---|---|---|
| V1 | **Store search audit** in the KSA/UAE App Store & Play Store (Arabic + English keywords). Download the top 10 apps and read 1–3★ reviews | Competitive gap is real | Founder time | Fewer than 2 strong Arabic-first competitors |
| V2 | **Landing page + waitlist** (AR/EN) with 2–3 value-prop variants (club rivalry / friends league / "prove you know football"). Run small paid social tests in KSA/UAE | Which message converts, A6 | Small ad test budget | Waitlist conversion ≥ 10–15% from targeted traffic |
| V3 | **Concierge MVP**: run a real prediction league for a matchweek cycle using a form + spreadsheet + WhatsApp/Telegram group; post weekly standings incl. club-vs-club table | A1, A2, A3 | Very low | ≥ 40% of participants still predicting in week 4, and organic requests to add friends |
| V4 | **Fake-door premium**: in the concierge league, offer a "Pro" stats pack at an indicated price (no charge taken) | A4 | Very low | ≥ 5% click-through among engaged users |
| V5 | **Data provider trial**: use free trials/tiers to track SPL/UAE fixtures for 2–3 matchweeks and compare timing and accuracy against official sources | A5 | Trial tiers | Final results available ≤ 10 min after FT, zero wrong results |
| V6 | **Sponsor conversations**: 5–10 conversations with local brands/agencies | A7 | Founder time | ≥ 2 expressions of interest |
| V7 | **Legal consult** (KSA + UAE) on free-to-play prediction with and without prizes | Legal envelope | Counsel fees | Written opinion |

**Go / no-go gate:** proceed to full MVP build if V3 retention and V2 conversion meet thresholds **and** V5
finds a viable provider. If V3 fails, revisit the proposition before writing production code.

---

## 2.5 Gaps & differentiation opportunities

1. **Arabic-first, GCC-league-first fan prediction game.** This appears under-served (pending V1).
2. **Club-vs-club fan rankings** as a first-class, shareable feature ("Al Hilal fans are #1 this week").
3. **Speed and transparency of settlement**, the direct answer to documented incumbent complaints.
4. **Respectful ad experience**: no forced video ads, no gambling ads.
5. **Trust / integrity signals**: every prediction timestamped on the server, visible lock time, and a public,
   explainable scoring breakdown.
6. **Groups as the acquisition engine**: one-tap invite links built for WhatsApp, the dominant messaging
   channel in the region **[verify usage stats]**.
7. **Tournament mode** for major events (AFC Asian Cup 2027 in KSA, UCL knockouts) with bracket-style
   predictions, as a V1.1 candidate.

---

## 2.6 Preliminary football-data provider scan (full evaluation in Part 13)

| Provider | Pricing (as found) | Coverage notes | Early view |
|---|---|---|---|
| **API-Football** | Free 100 req/day. Paid tiers reported at ~$19/$29/$39 per month for 7.5k/75k/150k req/day, all endpoints on all plans **[verify: figures from third-party blogs]** ([TheStatsAPI](https://www.thestatsapi.com/blog/thestatsapi-vs-api-football), [SportsAPI.com](https://sportsapi.com/api-directory/api-football/)) | Very broad league coverage **[verify SPL/UAE depth + latency]** | Strong MVP candidate on cost. Check the commercial terms and logo usage rights |
| **Sportmonks** | Free (2 leagues); Starter €29/mo (5 leagues); Growth €99/mo (30); Pro €249/mo (120); Enterprise custom. 14-day trial ([Sportmonks pricing](https://www.sportmonks.com/football-api/plans-pricing/), via search summary) **[verify]** | Claims 2,200+ leagues | Strong candidate. League-count pricing: 8 competitions exceeds the Starter plan (5), so Growth would be needed |
| **football-data.org** | Free tier: 12 competitions, 10 calls/min; paid tiers up to ~€199/mo ([football-data.org pricing](https://www.football-data.org/pricing)) | Free tier lacks SPL/UAE **[verify paid]** | Good for prototyping European leagues only |
| **Enterprise feeds (Stats Perform/Opta, Sportradar, Genius Sports)** | Custom quotes | Official-grade, low latency | Likely too costly for MVP. Relevant at scale or for official-partner deals |

**Key diligence items:** SPL/UAE Pro League/AFC coverage and result latency, how the provider handles corrections
(post-match score changes), status codes for postponed/abandoned matches, whether regulation-time scores are
exposed separately from AET/penalties, commercial display rights, and whether image assets (logos) are licensed.

---

## 2.7 Legal & app-store screen of proposed features (early flags)

| Feature | Risk | Why |
|---|---|---|
| Free predictions, virtual points, no prizes | **Low** | Standard game mechanics |
| Subscription for stats/cosmetics/ad-free | **Low** | Standard IAP. Must follow auto-renewal disclosure rules |
| Sponsor-provided prizes (free entry) | **Medium–High** | Apple 5.3.1/5.3.2: the developer must sponsor it, with official rules in-app stating Apple is not involved. Local promotion/competition laws apply, and GCC regulations need counsel |
| Paid entry + prizes | **Very High** | Likely constitutes gambling/commercial gaming. **Do not build** |
| Buying points / boosts that affect rank | **High** (integrity + possible gambling characterization) | Violates D-002 |
| Displaying betting odds / affiliate links to bookmakers | **High** in core markets | Brand damage, legal risk in KSA, and Google Play RMG policy |
| Official club crests / league logos | **Medium** | Trademark. Needs a license |

Sources: [Apple App Review Guidelines](https://developer.apple.com/support/downloads/terms/app-review-guidelines/App-Review-Guidelines-English-UK.pdf),
[Google Play Real-Money Gambling, Games, and Contests](https://support.google.com/googleplay/android-developer/answer/9877032?hl=en),
[GCGRA](https://www.gcgra.gov.ae/en/). *This is not legal advice, and a qualified lawyer must confirm it.*

---

## 2.8 Conclusion & recommendation

**Proceed to Phase 0 validation. Do not proceed yet to full-scale development.** The concept is viable and has a
credible wedge: Arabic-first club rivalry plus private groups. However, the category is mature in English markets,
consumer willingness to pay is low (the reference price is ~£2/month), and the key assumptions (A1–A5) are
unproven. The concierge league (V3) and the data-provider trial (V5) are the two experiments that most reduce risk.

Meanwhile, I can continue with Parts 3–22 (specification and architecture) in parallel, because they are needed
regardless of the validation outcome and don't require code.

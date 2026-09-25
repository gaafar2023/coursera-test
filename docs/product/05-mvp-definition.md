# PART 5 — MVP Definition

> Status: Draft v0.1 · 2026-09-24
> The MVP's goal is to **prove the weekly habit (A1), the group viral loop (A3) and willingness to pay (A4)**
> in KSA/UAE, while getting the prediction integrity foundation right from the start.

---

## 5.1 Review of your initial MVP hypothesis

| Your MVP item | Verdict | Change |
|---|---|---|
| Account creation | ✅ Keep | Apple, Google, and email. Apple Sign In is required on iOS whenever other third-party logins are offered **[verify current Apple guideline 4.8 wording]** |
| League selection | ✅ Keep | |
| Favorite club | ✅ Keep | Make it mandatory (≥1), because it feeds club leaderboards |
| Fixtures | ✅ Keep | |
| Score prediction | ✅ Keep | Add bulk "predict the whole matchweek" |
| Automatic result settlement | ✅ Keep | Build it to production grade: versioned, idempotent, SLA ≤ 15 min |
| Points | ✅ Keep | Simple MVP model (Part 7) |
| Basic leaderboard | ⚠️ **Expand** | Global + **club** + **country** + **private group**, weekly + season. Club boards are the differentiator |
| Profile | ✅ Keep | Basic stats. Detailed analytics go in the paid tier |
| Push notifications | ✅ Keep | Four notification types + granular controls |
| Basic subscription infrastructure | ⚠️ **Expand** | Both paid tiers are live at launch (D-003), so the entitlements system needs N tiers |
| English/Arabic foundation | ✅ Keep | Full RTL, Hijri/Gregorian display option, local time zones |
| Admin functionality | ⚠️ **Narrow** | A minimal operations console only. Full analytics dashboards come later |
| **Missing: private groups + invite links** | ➕ **Add** | This is the main growth engine (A3). Without it the MVP can't test virality |
| **Missing: shareable result cards** | ➕ **Add** | Cheap to build, and they drive organic acquisition via WhatsApp/Instagram/X |
| **Missing: account deletion & data export** | ➕ **Add** | Store requirement (account deletion) and GDPR-style rights, which apply from day one |
| **Missing: basic achievements** | ➕ **Add (small)** | 8–10 badges only. A full system comes in V1.1 |

## 5.2 MVP scope (MoSCoW)

### Must have
1. **Auth:** Apple, Google and email sign-in. Age gate (Q-11). Account deletion. Data export request.
2. **Onboarding:** language → leagues (multi-select) → club(s) (≥1, one marked "primary") → notification permission (with an explanation) → first prediction.
3. **Fixtures:** by date and by competition, favourite clubs first, kick-off in the user's local time zone.
4. **Prediction:** score stepper, editable until lock, **server-authoritative lock** (default: kick-off time, configurable per competition), countdown, prediction receipt.
5. **Bulk prediction:** predict all of a matchweek's fixtures on one screen.
6. **Settlement engine:** handles every match status (scheduled, live, finished, postponed, cancelled, abandoned, rescheduled, awarded), is idempotent and versioned, supports corrections, and meets the SLA ≤ 15 min p95.
7. **Scoring:** MVP model (Part 7). Scoring rules are stored as versioned configuration, not hard-coded.
8. **Leaderboards:** global, country, club (members' points), and "club vs club" (fan-base averages), each for the week and the season. Also private groups.
9. **Private groups:** create, invite via link/code (WhatsApp share sheet), join, leave, remove member (owner), group leaderboard. Free users can join up to N groups (Part 9 sets N).
10. **Profile:** username, country, favourite club, level/XP (basic), totals, accuracy, current and longest streak, prediction history.
11. **Notifications:** (a) prediction reminder before lock, (b) settled result, (c) rank movement weekly summary, (d) group invite accepted/joined. Every category can be toggled, and quiet hours are supported.
12. **Subscriptions:** Free / Plus / Pro, each monthly + annual, through App Store and Google Play billing, with server-side receipt validation, entitlements, restore purchases and required disclosures.
13. **i18n:** Arabic (RTL) + English, locale-aware numbers and dates, and all strings in translation files.
14. **Admin console (minimal):** manage competitions/fixtures, manually override a match status or score, re-run settlement, search users, suspend or ban users, view audit logs, manage subscription plans (display only), feature flags.
15. **Analytics:** the core event taxonomy (Part 16) plus crash reporting.
16. **Security baseline:** rate limiting, server time only, audit log of prediction changes, bot signals at sign-up.

### Should have (in MVP if time allows, otherwise first in V1.1)
- **Basic live match card:** live score, clock, "your prediction", potential points if the match ended now. (No event timeline.)
- **Shareable result card** (image generated on the device).
- **Basic achievements** (8–10 badges, e.g. first prediction, first exact score, 3-week streak).
- **Community prediction distribution**, shown **only after the user has submitted or after lock** (to avoid biasing picks).

### Could have
- Hijri date display option
- Dark mode (inexpensive with Flutter theming, and popular)

### Won't have in MVP (see the phase plan below)
Head-to-head challenges, comments/chat, match event timeline, advanced stats, a third paid tier, prizes, public groups, friends/follow graph (groups cover this), web app, tournament bracket mode.

## 5.3 Phase plan

| Feature | MVP | V1.1 | V2 | Future / experimental | Why here |
|---|:-:|:-:|:-:|:-:|---|
| Score prediction + settlement | ● | | | | Core loop |
| Club & club-vs-club leaderboards | ● | | | | Differentiator |
| Private groups + invites | ● | | | | Growth engine |
| Free + 2 paid tiers | ● | | | | Founder decision (D-003) and tests A4 |
| Live match card (score + potential points) | ◐ | ● | | | Adds excitement cheaply. The full event feed costs more API calls |
| Full achievement system, XP levels, weekly/monthly challenges | ◐ (basic) | ● | | | Needs usage data to tune |
| **Fan Store + Coins** (cosmetics, some subscriber-only; Part 8 §8.8) | ◐ (coins ledger only) | ● | | | Founder idea. The MVP records Coins so nobody loses earnings; the art budget arrives with V1.1 |
| Friends (follow/add) + friends leaderboard | | ● | | | Groups cover most of the need at first |
| Head-to-head challenges | | ● | | | Strong viral feature. It depends on the friends graph |
| Tournament mode (AFC Asian Cup, UCL knockouts) | | ● | | | Timed to events |
| Skill divisions (promotion/relegation) | | | ● | | Fixes the "hopeless" feeling at scale |
| Advanced scoring (upset bonus, confidence "banker" pick) | | | ● | | Only after the simple model is understood (Part 7) |
| Match event timeline, detailed stats | | | ● | | Data cost + design effort |
| Comments / reactions in groups | | | ● | | Moderation cost and legal exposure |
| Public groups / fan-club communities | | | ● | | Needs moderation tooling |
| B2B / company / university leagues (paid) | | | ● | | Needs a sales motion and a web admin |
| Sponsored challenges (no prizes) | | | ● | | Needs an audience first |
| Sponsored challenges **with prizes** | | | | ● ⚠️ | Legal review required (Part 17) |
| Web app | | | | ● | Mobile-first |
| AI insights / "why you were wrong" explainer | | | | ● | Bias and cost concerns |
| Official crests / club partnerships | | | | ● | Needs licensing (D-006) |

● = included · ◐ = partial / should-have

## 5.4 MVP competitions (approved 2026-09-24, D-005)
**8 competitions:** Saudi Pro League, UAE Pro League (ADNOC Pro League), Premier League, La Liga, Serie A,
Bundesliga, Ligue 1, UEFA Champions League.
Dropped from my original proposal: UEFA Europa League and AFC Champions League Elite. Fewer competitions means less
data cost and QA, which suits the small budget. Both are admin-configurable additions later, as are the local leagues of
the expansion markets (D-011).
**Knockout settlement rule (proposal, finalised in Part 7):** predictions settle on the **90-minute result
including stoppage time**. Extra time and penalties are ignored for scoring. This is the most common convention
and the simplest for users to understand.

## 5.5 MVP success criteria (evaluated 8 in-season weeks after launch; targets are assumptions)

| Criterion | Target |
|---|---|
| Activation: sign-up → first prediction | ≥ 70% |
| Week-4 retention (in-season) | ≥ 25% |
| WPU growth week over week (organic) | Positive for 6 of 8 weeks |
| Users in ≥ 1 private group | ≥ 30% |
| Free → paid conversion (Plus + Pro) | ≥ 2% |
| Settlement latency p95 | ≤ 15 min |
| Incorrect settlements reaching users | 0 uncorrected after 24 h |
| Crash-free sessions | ≥ 99.5% |

## 5.6 Paid tiers in MVP (Free + 2 paid, D-003)

Working names are **Plus** and **Pro**. Final names come in Part 18. Pricing research method is in Part 9, and no prices are set yet.

**Design rules for two paid tiers**
1. **Each tier has one clear reason to buy.** Plus = "a better, cleaner experience". Pro = "master your game and run your group".
2. **No tier affects global, country or club leaderboard points** (D-002).
3. **Pro includes everything in Plus.** No confusing partial overlaps.
4. **Basic pre-match information stays free** (see Part 4 §4.6).

| Feature | Free | Plus | Pro |
|---|:-:|:-:|:-:|
| All MVP competitions, unlimited predictions | ✅ | ✅ | ✅ |
| Basic pre-match info (table position, last 5, head-to-head summary) | ✅ | ✅ | ✅ |
| Global / country / club / group leaderboards | ✅ | ✅ | ✅ |
| Ads (native units only, never mid-prediction) | Yes | **No** | **No** |
| Private groups joined / created | Limited (e.g. 3 / 1) **[tune in Part 9]** | More (e.g. 10 / 3) | Highest (e.g. 25 / 10) |
| Max group size (as owner) | Standard | Standard | **Large** |
| Personal stats: accuracy by league/club, trends | Basic totals | ✅ | ✅ |
| Full prediction history with filters | Last 30 days | ✅ | ✅ |
| Cosmetics: profile frame, club-colour theme, supporter badge | — | ✅ | ✅ + exclusive Pro items |
| Advanced notification rules (e.g. "only my club + big matches") | — | ✅ | ✅ |
| **Pro:** deeper pre-match insights (home/away splits, goals trends) | — | — | ✅ |
| **Pro:** group admin tools: custom group scoring rules, group statistics, co-admins | — | — | ✅ |
| **Pro:** early access to new features | — | — | ✅ |

The limits shown ("e.g.") are placeholders to be tuned in Part 9 and during the beta.

**Engineering implication:** use a server-side **entitlements** model (feature → minimum tier), not
`if tier == "pro"` checks scattered through the code. Then limits and tier contents can change without an app release,
and a third tier can be added later if data supports it.

## 5.7 Club identity without official crests (D-006, delegated decision)

**Chosen approach for MVP:**
1. **Generated club badges:** a generic shield or circle shape (our own design), filled with the club's primary/secondary
   colours plus a 3-letter short code (e.g. HIL, NSR, ITT). The shape must not imitate the club's actual crest.
2. **Factual club and league names only** (e.g. "Al Hilal", "Premier League") to identify matches. No logos, mascots,
   slogans or official fonts.
3. **No player photos.** They carry separate image rights.
4. **Disclaimer** in the About page and store listing: "Not affiliated with or endorsed by any club, league or federation."
5. **App name, icon and store screenshots contain no club or league trademarks.** This is also important for app-store review.
6. **Data-provider contract check:** confirm whether logos are licensed for display in a commercial app (Part 13). If they are,
   we can switch per league through an admin flag, **after legal sign-off**.
7. **Later upgrade path:** club or league partnerships (also a revenue and marketing opportunity, see Part 20).

**Risk that remains:** the use of club colours and names is generally treated as descriptive, but this varies by
jurisdiction, so counsel must review it (Q-06). Cost of this approach: roughly one designer-day for the badge generator.

## 5.8 Explicit non-goals for MVP
- No iPad-optimised layout (phone layout scales up)
- No offline prediction queue (the prediction must reach the server before lock. The app says clearly if it didn't)
- No custom avatars beyond an uploaded photo (moderated) or an initials avatar

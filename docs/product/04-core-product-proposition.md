# PART 4 — Core Product Proposition

> Status: Draft v0.1 · 2026-09-24

---

## 4.1 Positioning statement

> **For** Arabic-speaking football fans in the Gulf **who** want to prove they know football and
> represent their club, **[App Name]** is a free football prediction game **that** turns every match
> into a friendly competition between friends and between fan bases. **Unlike** English-first
> prediction apps or betting-linked games, it is **Arabic-first, built around the Saudi and UAE
> leagues as well as Europe's top leagues, settles results within minutes, and never involves betting or money.**

## 4.2 Value proposition in one sentence per language (draft copy for testing in V2)

- **EN:** "Predict. Compete. Represent your club."
- **AR:** «توقّع، نافس، ومثّل ناديك» (*draft. Final wording will be written with a native copywriter*)

Alternative angles for the landing-page A/B test (Part 2, V2):
1. **Rivalry:** "Whose fans know football best?"
2. **Friends:** "Your group's prediction league, run for you."
3. **Mastery:** "Prove you know football, one prediction at a time."

## 4.3 Four product pillars

| Pillar | Promise to the user | What it means in product |
|---|---|---|
| **1. Effortless** | "Predict any match in seconds." | Onboarding in 3 taps. Score stepper [-] 2 [+]. Predict several matches at once from the fixtures list |
| **2. Tribal & social** | "Play with your people, for your club." | Club leaderboards, private groups, WhatsApp invites, shareable result cards |
| **3. Fair & fast** | "Every point is earned, and you'll know within minutes." | The server locks predictions, settlement takes ≤ 15 min (D-008), a transparent scoring breakdown, paid tiers never buy points (D-002) |
| **4. Respectful** | "No betting. No spam. No ad walls." | No odds or gambling ads, granular notification controls, no forced video ads |

## 4.4 What we are NOT

- **Not a betting or tipster product.** No odds, no stakes, no bookmaker partners.
- **Not fantasy football.** No squads, transfers or player management.
- **Not a live-score or news app.** We show just enough match data to predict well and follow your prediction.
- **Not an official club or league product.** We use no official branding unless licensed (D-006).

## 4.5 Proposition → feature map

| Promise | MVP feature(s) | Proof point for the user |
|---|---|---|
| Effortless | Guided onboarding, one-screen prediction, bulk matchweek predictions | First prediction in under 60 s |
| Tribal | Club leaderboard on home screen, "you earned X pts for your club" | Club's rank visible every visit |
| Social | Private groups, invite links, group leaderboard | Friends' names on the board |
| Fair | Server-side lock countdown, immutable prediction receipt, points breakdown | "Locked at 19:30:00 (server time)" shown on the prediction |
| Fast | Settlement worker + result push notification | "Full time: you scored +5" within minutes |
| Respectful | Notification settings screen, native-only ads for free users | Settings reachable in 2 taps |

## 4.6 Why users pay (without affecting fairness)

Paid value comes from **insight, convenience, identity and group power**. Buying better competitive outcomes is never on offer.

| Value type | Examples | Fairness check |
|---|---|---|
| Insight | Personal analytics, accuracy by league/club, deeper pre-match stats | ⚠️ Basic pre-match info (table position, last-5 form, head-to-head summary) stays **free** so paying users don't get a meaningful information advantage |
| Convenience | Ad-free, more groups, advanced notification rules | ✅ No effect on points |
| Identity | Profile frames, club-colour themes, supporter badges (cosmetic) | ✅ No effect on points |
| Group power | Larger groups, custom group scoring, group statistics | ✅ Affects only that private group, which its members join knowingly. The global/club boards always use standard scoring |

Tier details are in Part 5 §5.6 (MVP scope) and Part 9 (full strategy and pricing research).

## 4.7 North-star metric

**Weekly Predicting Users (WPU):** unique users who submit at least one prediction in a given matchweek.

Why this metric: it captures the core habit (returning every matchweek), rises with network effects
(groups bring players back), and cannot be inflated by installs or passive opens.

Supporting metrics:
- Predictions per WPU
- % of WPU in at least one private group
- Settlement latency p95
- Free → paid conversion
- Week-4 retention

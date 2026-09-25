# PART 9 — Subscription & Monetization

> Status: Draft v0.1 · 2026-09-25
> Constraints: Free + 2 paid tiers (D-003, D-015). Paid tiers never affect points (D-002). No prizes or wagering (D-001).
> Very small budget (D-016). **No final prices are set here.** §9.4 defines how we will find them.

---

## 9.1 Monetization principles

1. **Integrity is the product.** Nothing purchasable changes points, lock times, leaderboard maths or XP.
2. **Pay for insight, convenience, identity and group power** (Part 4 §4.6).
3. **The free product must be complete and enjoyable.** Free users make up the leaderboards and groups that paying users value.
4. **No dark patterns.** Prices are clear, cancelling is easy, and renewal terms are disclosed.
5. **Diversify.** Consumer subscriptions in this category have a low price point (Superbru Premium is ~£2/month, see Part 2), so sponsorship and B2B must share the load over time.

## 9.2 Revenue streams evaluated

| Stream | Fit | Timing | Advantages | Disadvantages / risks | Recommendation |
|---|---|---|---|---|---|
| **Subscriptions (Plus/Pro)** | High | MVP | Recurring, aligned with the product, no ads needed | Low price point, needs scale | **Primary from day one** |
| **Advertising (free tier)** | Medium | MVP (flagged, off at first) | Monetises non-payers | Hurts UX (a documented Superbru complaint); gambling-ad risk | **Native units only, strict category blocklist** (no gambling, betting, alcohol, dating). Turn on only once there are ~5k+ WPU, when revenue would be meaningful |
| **Brand-sponsored challenges** | High in GCC | V2 | Premium CPMs, natural fit, no UX damage | Needs audience scale, and prizes need legal review | **Second pillar.** XP/badge rewards first, prizes only after counsel sign-off |
| **Club / league partnerships** | Medium–High | V2+ | Official logos (solves D-006), credibility, distribution | Slow sales cycles, exclusivity demands | Pursue through the founder's club relationships (D-018) |
| **Media partnerships** (sports sites, broadcasters) | Medium | V2 | Distribution + revenue share, embeddable widgets | Dependency, brand dilution | Opportunistic |
| **B2B private leagues** (companies, universities) | Medium | V2 | Higher ARPU per organisation, seasonal renewals | Needs a web admin and invoicing | Pilot with 3–5 organisations from the founder's network |
| **Premium analytics / data** | Low | Future | – | Small market, privacy concerns | Not now |
| **Affiliate (e.g. merchandise, tickets)** | Low–Medium | Future | Easy to add | Must never include betting | Merch/tickets only, with legal check |
| **Bookmaker affiliates / odds** | ❌ | Never | – | Illegal or haram in the core market, app-store risk, brand damage | **Rejected** |

## 9.3 Tier design (approved split, D-015)

The full feature matrix is in Part 5 §5.6. In short:

| | **Free** | **Plus** | **Pro** |
|---|---|---|---|
| Promise | Full game, all competitions | "A cleaner, better experience" | "Master your game and run your group" |
| Key benefits | Unlimited predictions, all boards, basic pre-match info, limited groups | Ad-free, personal stats, full history, more groups, cosmetics, advanced notifications | Everything in Plus + deeper pre-match insights, group admin tools (custom group scoring, large groups, co-admins), exclusive cosmetics, early access |
| Target persona | P4 Social, P1 Rival | P1 Rival, P5 Expat | P3 Analyst, P2 Organizer |
| Billing | – | Monthly + annual | Monthly + annual |

**Pricing structure rules (the numbers themselves come from §9.4):**
- Pro should cost **roughly 2×** Plus, so Plus looks like the easy choice and Pro the premium one.
- Annual plans are discounted **~30–40%** against 12 × monthly, and presented as "months free".
- Prices are set **per storefront** (SAR for KSA, AED for UAE) using the store's price points, not converted from USD.

**Group limits (initial values, then tuned with data):**

| | Free | Plus | Pro |
|---|---:|---:|---:|
| Groups you can join | 3 | 10 | 25 |
| Groups you can create | 1 | 3 | 10 |
| Max members in a group you own | 50 | 50 | 200 |

A Free organizer's group can still contain paying members and vice versa. Membership in a group never depends on the member's tier.

## 9.4 Pricing research methodology (before setting any price)

| Step | Method | When | Cost |
|---|---|---|---|
| 1 | **Competitive anchors:** record the prices of comparable apps in the KSA/UAE storefronts (prediction games, football stats apps, sports streaming add-ons) | Phase 0 | Free |
| 2 | **Van Westendorp survey** (too cheap / cheap / expensive / too expensive) with 100–200 fans from the founder's communities, in Arabic, separately for Plus and Pro | Phase 0 (during the concierge league) | Free (Google Forms / Typeform free tier) |
| 3 | **Fake-door test** in the concierge league: "Pro stats pack, X SAR/month", measuring click-through at 2–3 price points | Phase 0 | Free |
| 4 | **Beta price tests:** 2–3 price points by storefront or cohort, using store tools or the billing platform's experiments | Beta | Included in tooling |
| 5 | **Post-launch review** at 8 weeks: conversion × retention × ARPU per price point | Launch + 8 weeks | Analytics |

**Launch markets:** KSA and UAE storefronts. Other Arab markets follow D-011, each with its own local price points (purchasing power differs a lot between e.g. Qatar and Egypt).

## 9.5 Free trial strategy

| Option | Advantages | Disadvantages | Recommendation |
|---|---|---|---|
| No trial | Simple, immediate revenue | Lower conversion for an unknown brand | – |
| **7-day trial on annual plans only** | Encourages annual (better retention), common pattern | Some trial abuse | **Recommended for Pro annual and Plus annual** |
| 1-month trial | High uptake | Delays revenue, many forget and churn angrily | No |
| Event-triggered trial (e.g. "free Pro for the Asian Cup") | Strong acquisition hook | Revenue delay | Use for marketing moments |

Trial rules: one trial per store account (enforced by the stores). A clear reminder is sent 24 h before the trial ends, which builds trust and reduces refunds.

## 9.6 Upgrade paths & paywall placement

Paywalls appear **in context, when the user wants something**, never as a pop-up on app open.

| Trigger | Paywall shown | Offered tier |
|---|---|---|
| Tries to join a 4th group / create a 2nd | "Unlock more groups" | Plus (Pro shown) |
| Taps "See your accuracy by league" | Stats preview (blurred sample of their own data) | Plus |
| Organizer taps "Custom scoring" or hits the member limit | Group-admin paywall | Pro |
| Taps "Deep insights" on the match screen | Insight preview | Pro |
| Sees a Pro frame on the leaderboard | Cosmetic preview | Plus/Pro |
| After a great week ("You were top 5%!") | Soft upsell card on the results screen, max once a week | Plus |

**Upgrade and downgrade handling:** Plus → Pro is prorated by the store. On a downgrade, groups over the limit are kept but become read-only for new invites, and **no data is ever deleted**.

## 9.7 Retention strategy (subscribers)

- **Value reminders:** a monthly "your Pro month" summary (accuracy trend, best pick, group stats).
- **Billing retry + grace period** enabled in both stores, so failed payments don't instantly remove access.
- **Cancellation flow:** one-screen exit survey (optional), with an optional pause or monthly→annual switch offered only once.
- **Win-back offers** to lapsed subscribers at season start or before big tournaments, through the store's promotional/offer codes.
- **Seasonality:** push annual plans at season start (August) and before major tournaments. Expect monthly churn in June–July.

## 9.8 Billing implementation (lean)

| Option | Advantages | Disadvantages | Cost | Recommendation |
|---|---|---|---|---|
| **RevenueCat** (billing SDK + backend) | Cross-platform receipts, entitlements, webhooks, paywall experiments. Saves weeks of work | Third-party dependency | Free up to **$2,500 monthly tracked revenue**, then 1% ([RevenueCat pricing](https://www.revenuecat.com/pricing), via search summary **[verify]**) | **Recommended** for a solo/lean build |
| Native StoreKit 2 + Play Billing + own server validation | No fees, full control | Significant work, edge cases (refunds, grace, family sharing) | Engineering time | Later, if fees ever matter |

Our backend remains the **source of truth for entitlements** (Part 6 §6.4). RevenueCat webhooks update it, so a vendor change doesn't touch app logic.

**Store fees (verify at enrolment):**
- Apple's Small Business Program charges **15%** for developers under USD 1M proceeds; the standard rate is 30%.
- Google Play charges **15%** on auto-renewing subscriptions.

Sources: [RevenueCat blog](https://www.revenuecat.com/blog/engineering/small-business-program), [Appbot](https://appbot.co/blog/app-developers-apple-google-small-business-programs/). **Action:** enrol in Apple's Small Business Program before the first sale.

**Required disclosures on the paywall** (Apple and Google requirements; counsel to review): price and period, auto-renewal, how to cancel, links to terms and privacy, and a "Restore purchases" button.

## 9.9 Unit economics sketch (illustrative only; every number is an assumption)

```
Monthly subscription revenue  =  WPU × paid_conversion × blended_net_price
Example: 20,000 WPU × 3% × (price P after 15% store fee)
        = 600 subscribers × 0.85P
```
The real question for the lean budget: **can subscriptions cover the fixed costs** (data API + infra + developer accounts, Part 20) **at a small scale?** We will fill in real prices once §9.4 is done. The target is break-even on running costs with fewer than 10k WPU.

## 9.10 Legal & store flags
- Subscription benefits must be delivered as described. Changing a tier's contents needs notice to users.
- No prizes of any kind are tied to paid tiers. That would create a "pay to win" or gambling characterisation.
- Sponsored challenges with prizes are **blocked until counsel signs off** (D-017), and must follow Apple 5.3 rules.
- Auto-renewal consumer rules differ by country. KSA and UAE counsel should review the paywall copy.

# PART 20 — Cost Model

> Status: Draft v0.1 · 2026-09-25 · Currency: USD (approx.)
> **Every figure is an estimate or assumption** unless a source is given. Prices change, so re-check them before committing.
> One-time costs are separated from recurring ones.

---

## 20.1 Known platform fees (sourced)

| Item | Cost | Type | Source |
|---|---|---|---|
| Apple Developer Program | $99 / year | Recurring | [Magora](https://magora-systems.com/apple-developer-fee/), [SplitMetrics](https://splitmetrics.com/blog/google-play-apple-app-store-fees/) |
| Google Play developer account | $25 once | One-time | same |
| Store commission on subscriptions | 15% (Apple Small Business Program; Google subscriptions) | % of revenue | Part 9 §9.8 |
| Supabase | $0 (Free) → $25/mo (Pro) + usage | Recurring | Part 11 |
| API-Football | ~$19/mo (7,500 req/day tier) **[verify]** | Recurring | Part 2 §2.6 |
| RevenueCat | Free up to $2.5k monthly tracked revenue, then 1% | Recurring (later) | Part 9 |
| FCM push, Crashlytics | Free | – | Google |
| PostHog | Free tier **[verify limits]** | – | Part 16 |
| Pen test (startup scope: mobile + API) | ~$4,000–13,000 | One-time (per test) | [Startup Defense](https://www.startupdefense.io/blog/penetration-testing-cost), [Budget Security](https://budgetsecurity.com/blog/mobile-app-penetration-testing-cost/) |
| Freelance Flutter developer | ~$15–40/h (South Asia/Africa), ~$30–45/h (Eastern Europe, senior), $40–75/h typical vetted global | Variable | [index.dev](https://www.index.dev/blog/flutter-developer-hourly-rates), [Lemon.io](https://lemon.io/rate-calculator/flutter-developers/) |

## 20.2 Three scenarios

### Scenario A — Lean MVP (recommended for the founder's budget)
**How:**
- The founder + **Claude writes most of the code** in sessions (backend, Flutter app, admin, tests).
- A **part-time freelance Flutter developer** (~60–120 h) reviews the code, tests on real devices and handles store releases.
- A **freelance designer** (~40–80 h) does the brand and key screens.
- Free tiers wherever possible.

| Cost item | One-time | Monthly (at launch) | Assumptions |
|---|---:|---:|---|
| Development (freelance review/testing/release) | $3,000–8,000 | – | 60–120 h × $30–60/h |
| UI/UX + brand design | $1,500–5,000 | – | 40–80 h freelancer, or a design contest |
| Illustrations for the Fan Store (V1.1) | $800–2,500 | – | Modular avatar set (Part 8 §8.8) |
| Football data API | – | ~$19 | API-Football Pro tier |
| Cloud (Supabase Pro) | – | ~$25–50 | < 10k WPU |
| Auth, push, analytics, crash | – | $0 | Free tiers |
| Monitoring / uptime | – | $0–10 | Free tier |
| Apple + Google accounts | $25 | ~$8 ($99/yr) | – |
| Domain(s) | $15–60/yr | – | .com; .sa/.ae may need a local entity **[verify]** |
| Email (support@) | – | $0–7 | Free tier or 1 business mailbox **[verify]** |
| Legal (T&Cs, privacy, opinion, trademark) | **[counsel quote]** | – | Founder has counsel. Trademark filing fees in KSA + UAE **[counsel]** |
| Pen test | $4,000–8,000 | – | Minimum scope. Can follow a very small beta if budget is tight, but **before public launch** |
| Customer support | – | $0 | Founder handles it + FAQ |
| Maintenance | – | 10–20 h/mo freelance (~$300–1,000) or founder + Claude | After launch |
| **Total (excluding legal)** | **≈ $9,000–23,000** | **≈ $55–100 (+ optional maintenance)** | |

**Honest caveats:**
- AI-assisted development still needs **a human to test on real phones, manage store accounts, and make judgement calls**. That's the founder's time + the freelancer.
- The biggest single cost risk is the **pen test**. Skipping it is not recommended for an app with accounts and payments.

### Scenario B — Professional startup MVP
**How:** a small freelance or boutique team for ~4–5 months: Flutter dev, backend dev, designer, part-time QA, part-time PM.

| Cost item | One-time | Monthly |
|---|---:|---:|
| Development (≈ 1,200–1,800 h × $35–55/h) | $42,000–99,000 | – |
| UI/UX + brand | $6,000–15,000 | – |
| QA | $4,000–10,000 | – |
| Pen test | $6,000–13,000 | – |
| Infra + data + tools | – | $100–300 |
| Maintenance | – | $2,000–5,000 |
| **Total** | **≈ $58,000–137,000** | **≈ $2,100–5,300** |

### Scenario C — Production-scale product (after product-market fit)
**How:** an in-house or agency team of ~6–10 (mobile ×2, backend ×2, QA, designer, PM, data/growth), 24/7 monitoring, and possibly an enterprise data feed.

| Cost item | Annual (approx.) |
|---|---:|
| Team (GCC salaries are higher than remote) | $400,000–1,000,000+ **[assumption; depends on location]** |
| Infra at 100k–1M WPU (Part 11 §11.7) | $12,000–60,000+ |
| Enterprise football data (official feeds) | **[quote]**, typically much higher than API-Football |
| Security (annual pen test, tooling) | $15,000–40,000 |
| Marketing & community | Separate budget |

## 20.3 Break-even sketch (Scenario A running costs)
Running costs are roughly **$55–100/month**. After a 15% store fee, the app covers these once it earns about **$65–120/month in subscriptions**. For example, that's ~25–50 subscribers at a price around $2.5–3/month, which is the Superbru-like reference price in Part 2. **Real prices come from the Phase 0 price test** (Part 9 §9.4).

## 20.4 Recommendation
1. **Phase 0 costs almost nothing.** Do it first (the test league + legal opinion).
2. If validation succeeds → **Scenario A**, with the budget prioritised as: design > freelance device testing/release > pen test.
3. Move to **Scenario B-type spending** only if traction justifies it (e.g. sponsor interest, or growth beyond ~10k WPU).

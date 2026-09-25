# PART 15 — Admin Dashboard

> Status: Draft v0.1 · 2026-09-25
> A web console (Next.js, Part 11) used by the founder and, later, a small operations team.
> **MVP = operations only.** Business analytics dashboards come later; until then we use the analytics tool's built-in dashboards (Part 16).
> Every write goes through audited RPCs (Part 14 §14.7).

---

## 15.1 Modules

| Module | MVP | Capabilities | Role |
|---|:-:|---|---|
| **Match Operations** | ● | Live list of today's fixtures with provider status vs our status; settlement lag; HOLD queue (anomalies); override status/result (with reason); re-settle; freeze/unfreeze; set derby flag; change lock offset per competition | Operator |
| **Competitions & Clubs** | ● | Enable/disable competitions; edit Arabic/English names, short codes, badge colours; map provider IDs; season setup + scoring ruleset assignment | Operator |
| **Users** | ● | Search by username/email/ID; profile, predictions (with revision history), points ledger, devices, subscription status; export data; delete account on request | Support |
| **Moderation** | ● | Reports queue; rename username; hide from boards; warn/suspend/ban/restore; anti-cheat flags queue (Part 14 §14.5) with evidence | Admin |
| **Groups** | ● | Search groups; view members; remove offensive group names; archive | Support |
| **Subscriptions** | ● | View a user's entitlement history and store events (read-only); plan display config; entitlement matrix editor (Part 6 §6.4) | Admin |
| **Feature Flags & Config** | ● | Toggle flags, % rollout, min app version, maintenance banner | Admin |
| **Audit Log** | ● | Search/filter all admin actions (read-only) | Admin |
| **Notifications** | ◐ | View the outbox; send an operational broadcast (e.g. "results delayed"). Marketing broadcasts come in V1.1 with segment targeting + frequency caps | Admin |
| **Achievements & Challenges** | V1.1 | Create and edit achievements, weekly/monthly challenges, sponsored challenges (Charter check required, Part 17) | Admin |
| **Fan Store** | V1.1 | Items, prices, tier requirements, seasonal availability, monthly subscriber grants | Admin |
| **Promotions** | V1.1 | Store offer codes (trial/win-back) management links, event campaigns | Admin |
| **Analytics dashboards** | V1.1 | See §15.3 | Admin |
| **Admin management** | ● | Invite admins, assign roles, force MFA, revoke | Superadmin |

## 15.2 Key operational screens (MVP)

**Match Day Control (home screen)**
```
Today · 14 fixtures · 3 live · 9 settled · 1 HOLD ⚠️ · avg settlement lag 6m (SLA 15m)
┌──────────────────────────────────────────────────────────────────────────────┐
│ SPL  Al Hilal 2–1 Al Ittihad   FT   settled v1   6m    [view] [re-settle]    │
│ EPL  Arsenal – Chelsea         LIVE 67'   –                                  │
│ UCL  Real Madrid 1–1 Inter     ABD  ⚠️ HOLD: abandoned, choose:              │
│        [resumed later] [replay] [result stands] [void]  reason: [________]   │
└──────────────────────────────────────────────────────────────────────────────┘
Alerts: settlement lag > 10 min · provider errors · unknown status codes · reconciliation mismatches
```

**Result override flow:** open the fixture → enter the 90-min score (and the final score for display) → mandatory reason → preview the impact ("1,240 predictions change; 310 users gain, 95 lose points") → confirm (4-eyes above 1,000 affected users, V1.1) → new result version → re-settlement → users notified.

**User detail:** a timeline of sign-up, predictions (each with revision history and server timestamps), settlements, moderation actions and subscription events. Used for disputes: "I predicted before kick-off" can be answered with evidence in seconds.

## 15.3 Analytics (V1.1 in-console; MVP uses PostHog/Firebase dashboards)

| Area | Metrics |
|---|---|
| Engagement | DAU, WAU, MAU, **WPU (north star)**, DAU/MAU, sessions per user |
| Retention | D1/D7/D30 cohorts, week-4 in-season retention, returning-after-break rate |
| Prediction activity | Predictions per WPU, % of fixtures predicted per competition, bulk-predict usage, edits before lock, time-to-lock distribution |
| Accuracy | Average points per prediction, exact %, result % (by competition and club), upset rates |
| Content popularity | Most-followed competitions and clubs, most-predicted fixtures, derby participation |
| Social | Groups created, invites sent/accepted, K-factor, % of WPU in groups |
| Monetization | Paywall views → trial → paid conversion (by trigger), MRR, ARPPU, churn (monthly/annual), refunds, tier mix |
| Notifications | Sent, opened, disabled-by-category, and pushes per user per day (checks the caps) |
| Integrity & ops | Settlement lag p50/p95, corrections, HOLDs, anti-cheat flags, bans |
| Store (V1.1) | Coins earned vs spent, items bought, exclusive-item upgrade conversions |

## 15.4 Build approach (lean)
- A Next.js app on free/low-cost hosting, with Supabase Auth (admin roles + MFA) and admin RPCs. No direct table editing, which is why we don't use Supabase Studio for production operations.
- Charts use the analytics tool's embeds, or simple SQL views over aggregate tables.
- **Estimated MVP effort:** ~2–3 weeks for one developer for the ● modules **[estimate]**.

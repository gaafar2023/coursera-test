# PART 22 — Product Requirements Document (PRD): MVP

> Status: **v1.0 draft for founder sign-off** · 2026-09-25
> Product (working name): **KooraCall** project. The public name is decided by the community vote (FootGooal / Mudarraj / KooraCall, D-039).
> This PRD consolidates Parts 1–21. When documents conflict, the **Decision Log (Part 1)** wins, then this PRD.

---

## 1. Summary
A **free, Arabic-first football prediction game** for iOS and Android. Fans predict match scores, earn points for accuracy, and compete with friends, their country and **other clubs' fans**.
- **No gambling, ever** (No-Gambling Charter, Part 17).
- Launch markets: **KSA + UAE** (users 16+), then other Arab markets.
- Revenue: Free + **Plus** + **Pro** subscriptions (cosmetics, statistics, convenience, group tools). Paid tiers **never affect points**.

## 2. Goals & success metrics (8 in-season weeks after launch)

| Goal | Metric | Target |
|---|---|---|
| Habit | Weekly Predicting Users (north star), week-4 retention | WPU growing 6 of 8 weeks; W4 retention ≥ 25% |
| Activation | Sign-up → first prediction | ≥ 70% |
| Virality | Users in ≥ 1 group; K-factor | ≥ 30%; ≥ 0.3 |
| Trust | Settlement p95; uncorrected wrong settlements | ≤ 15 min; 0 after 24 h |
| Business | Free → paid | ≥ 2% |
| Quality | Crash-free sessions | ≥ 99.5% |

## 3. Scope

**In the MVP** (Part 5):
- Auth (Apple/Google/email), 16+ age gate, account deletion, data export
- Onboarding (language → leagues → clubs → first prediction)
- 8 competitions
- Fixtures, match details, single + bulk prediction
- Server-authoritative lock
- Automatic settlement (5/3/2/0, 90-min rule)
- Leaderboards: global, country, competition, club fans, club vs club, groups (week + season)
- Private groups with invites
- Profile & basic stats
- Basic XP/levels/streaks, 10 badges, Coins ledger
- Notifications with controls
- Free/Plus/Pro subscriptions
- AR/EN + RTL
- Admin console (operations)
- Analytics
- Security baseline

**Should-have** (if time allows): live match card, share cards, community distribution after submission.

**Out of the MVP:**
- Fan Store UI (V1.1)
- Friends and head-to-head challenges (V1.1)
- Challenges (V1.1)
- Comments/chat and public groups (V2)
- Advanced scoring (V2)
- Web app
- **Prizes, wagering or selling currency: never**

## 4. Functional requirements

IDs are used in the backlog and tests.

### 4.1 Account & onboarding (AUTH, ONB)
- **AUTH-1** Sign up / sign in with Apple, Google or email.
- **AUTH-2** 16+ age confirmation; under-16 blocked.
- **AUTH-3** Delete account in-app (+ web link), completed ≤ 30 days.
- **AUTH-4** Request a data export (JSON).
- **ONB-1** Language choice (AR default in KSA/UAE).
- **ONB-2** Choose ≥ 1 competition.
- **ONB-3** Choose ≥ 1 club and mark one as primary.
- **ONB-4** Make a first prediction before sign-in; it is submitted after authentication if still before lock.
- **ONB-5** Notification pre-prompt.

### 4.2 Fixtures & prediction (FIX, PRED)
- **FIX-1** Fixture list by date and competition, favourites first, in local time.
- **FIX-2** Match details: table position, last 5, H2H, goals for/against (free). Deeper insights for Pro.
- **PRED-1** Score stepper 0–15 per team; no default value.
- **PRED-2** Unlimited edits before lock. Every edit is stored as a revision with server time.
- **PRED-3** Lock at `kickoff − offset` (default 0), enforced by the DB clock. Requests at or after the lock are rejected with `PREDICTION_LOCKED`.
- **PRED-4** Bulk prediction for a matchweek, with per-item results.
- **PRED-5** Receipt showing the server timestamp and revision history.
- **PRED-6** Other users' predictions are visible only after lock. The community distribution is shown only after the user has predicted, or after lock.
- **PRED-7** Postponed-before-kick-off → prediction kept and reopened with a new lock. No new date within 21 days → void.

### 4.3 Settlement & scoring (SET)
- **SET-1** Score = 5 exact / 3 result + goal difference / 2 result / 0 wrong, on the 90-min result.
- **SET-2** Void rules: cancelled, awarded, walk-over, postponed beyond 21 days, or an admin void of an abandoned match.
- **SET-3** Settle ≤ 15 min p95 after the provider reports FT, with a 2-min debounce.
- **SET-4** Corrections within 72 h create a new result version; deltas are applied and users notified. After 72 h, admin only.
- **SET-5** Idempotent, reproducible settlement. Ledgers are append-only.
- **SET-6** Unknown or inconsistent data → HOLD + admin alert.

### 4.4 Leaderboards (LB)
- **LB-1** Scopes: global, country, competition, club fans, club vs club, group. Periods: week (Tue 04:00 Riyadh) and season.
- **LB-2** Ties: points → exacts → correct results → shared rank.
- **LB-3** Club vs club = mean points of active fans (≥ 3 settled, account ≥ 7 days), min 30 active fans. Primary club changeable once per season.
- **LB-4** Own row pinned, rank movement, percentile.
- **LB-5** Accounts flagged by anti-cheat are hidden from public boards.

### 4.5 Groups (GRP)
- **GRP-1** Create a group (name 3–40 characters); owner role.
- **GRP-2** Invite by link/code via the share sheet (WhatsApp first). Deferred deep link through install.
- **GRP-3** Join, leave; owner removes members; ownership transfers if the owner leaves.
- **GRP-4** Group limits by tier (Part 9 §9.3).
- **GRP-5** Pro: custom group scoring (affects that group only), co-admins, large groups.

### 4.6 Profile & gamification (PROF, GAM)
- **PROF-1** Profile: username, country, primary club badge, tier badge, level/XP, totals, accuracy, streaks, ranks, badges, history (Free: 30 days).
- **GAM-1** XP and levels (Part 8 §8.2).
- **GAM-2** Participation and result streaks, plus 1 free freeze per month.
- **GAM-3** 10 MVP badges.
- **GAM-4** A Coins ledger mirroring points 1:1 (store UI in V1.1).

### 4.7 Notifications (NOTIF)
- **NOTIF-1** Categories: reminder, result, weekly rank, group activity, club-vs-club (opt-in), marketing (opt-in).
- **NOTIF-2** Quiet hours, per-category toggles, caps (≤ 1 reminder per matchday, ≤ ~3 pushes per day).
- **NOTIF-3** Inbox with deep links.

### 4.8 Subscriptions (SUB)
- **SUB-1** Plus and Pro, monthly and annual, through App Store / Google Play (RevenueCat).
- **SUB-2** Server-side entitlements, never trusted from the client.
- **SUB-3** Contextual paywalls, disclosures, restore purchases, and a 7-day trial on annual plans.
- **SUB-4** Downgrades never delete data.
- **SUB-5** No entitlement affects points, locks or public leaderboards (automated test).

### 4.9 Admin (ADM)
- **ADM-1** Match-day control, HOLD queue, override result/status (with a reason and impact preview), re-settle, freeze.
- **ADM-2** Competitions/clubs management, including Arabic names and badge colours.
- **ADM-3** User search, prediction history evidence, moderation ladder.
- **ADM-4** Feature flags, entitlements editor, min app version.
- **ADM-5** Audit log (read-only). MFA and RBAC.

### 4.10 Localisation & compliance (L10N, CMP)
- **L10N-1** Full AR (RTL) + EN, with no hard-coded strings; local time zones; optional Hijri display.
- **CMP-1** No-Gambling Charter enforced: vocabulary lint, no odds, no prizes, no currency sales.
- **CMP-2** "Not affiliated" disclaimer; generated club badges only.
- **CMP-3** Privacy labels, data-safety form, legal documents in AR/EN.

## 5. Non-functional requirements
Full detail is in Part 6 §6.6, Part 11 and Part 14. Key points:
- Prediction submit p95 < 300 ms; leaderboard read p95 < 200 ms.
- 99.5% availability.
- Handles a 10× lock-time spike.
- RLS on every table.
- Append-only integrity tables.
- TLS everywhere, encryption at rest.
- WCAG AA contrast; dynamic type up to 200%.

## 6. User stories (key features)

Format: **Story** · Acceptance criteria (AC) · Edge cases (EC) · Failure conditions (FC).

**US-01 Choose my club**
As a football fan, I want to select my favourite club so that the app prioritises its matches and I compete for its fan base.
- **AC:**
  - I can pick ≥ 1 club and star exactly one as primary.
  - The home screen shows my primary club's next match first.
  - My points count toward my club on the club-vs-club board.
- **EC:**
  - Selecting a single club makes it primary automatically.
  - Changing the primary club a second time in a season is refused with an explanation.
  - A club relegated from a followed league stays selectable.
- **FC:** club list fails to load → retry banner, and onboarding can't continue without ≥ 1 club.

**US-02 Predict a match**
As a fan, I want to predict a score in seconds so that I can compete without effort.
- **AC:**
  - Stepper 0–15, Save.
  - The receipt shows the server time.
  - I can edit until the lock; the lock countdown uses server time.
- **EC:**
  - Submit 1 ms before lock → accepted. At or after lock → rejected with a clear message.
  - Double tap → one revision (idempotency).
  - Kick-off moved earlier → the lock moves and I'm notified.
- **FC:**
  - Offline → "Not saved" with retry. It never shows as saved without server confirmation.
  - A device clock set in the past has no effect.

**US-03 Get my result fast**
As a fan, I want my points within minutes of full time so that I can celebrate or argue while the match is fresh.
- **AC:** push within 15 min (p95) of FT, showing the score, my pick, points and the rule applied.
- **EC:**
  - AET/PEN is scored on 90 minutes.
  - Postponed → kept/reopened.
  - Abandoned → HOLD until an admin decides.
  - A provider correction within 72 h → re-scored + notification.
- **FC:** provider outage → "Results delayed" banner, then settlement when data returns. Duplicate provider events never double-award points.

**US-04 Create a group and invite friends**
As an organiser, I want to create a group and share it on WhatsApp so that my friends join without admin work.
- **AC:** create in < 1 min; the invite link opens the app, or the store and then auto-joins after install; the group board updates after each settlement.
- **EC:**
  - Invitee already a member → opens the group.
  - Link expired → message.
  - Group at its member limit → owner prompted (Pro for large groups).
  - Owner leaves → ownership transfers.
- **FC:**
  - Deep link lost after install → join-by-code fallback.
  - Code brute-forcing → rate-limited.

**US-05 Club vs club**
As a rival fan, I want to see which fan base predicts best so that I can help my club win.
- **AC:**
  - A weekly and season table of clubs by mean points of active fans.
  - My contribution is shown ("You 14 vs club avg 9.2").
- **EC:**
  - Club below 30 active fans → "not enough fans yet (12/30)" + invite CTA.
  - New accounts (< 7 days) excluded.
  - Ties share a rank.
- **FC:** anti-cheat-flagged accounts are excluded from the calculation.

**US-06 Leaderboards**
As a competitive fan, I want to see my rank in several leaderboards.
- **AC:**
  - Scopes and periods as in LB-1.
  - My row is pinned.
  - Rank movement and percentile are shown.
- **EC:** ties (LB-2); the weekly reset happens on Tuesday 04:00 Riyadh for everyone.
- **FC:** a slow board shows cached data with an "updating" indicator.

**US-07 Subscribe**
As an engaged fan, I want to upgrade to Plus or Pro to get stats, cosmetics and group tools without it being unfair to others.
- **AC:**
  - Paywall with clear prices and disclosures; purchase through the store.
  - Features unlock within seconds after server validation.
  - Restore works.
- **EC:**
  - Trial ends → renew or downgrade.
  - Refund → entitlements revoked.
  - Downgrade → excess groups become read-only for invites, and no data is deleted.
- **FC:**
  - Pending or failed purchase → a clear state with no double charge.
  - The client can never grant itself an entitlement.

**US-08 Control notifications**
As a user, I want to control which notifications I get so that the app doesn't annoy me.
- **AC:** per-category toggles, quiet hours, a caps-respecting reminder batch.
- **EC:** OS permission denied → in-app inbox only, with a gentle re-prompt path in Settings.
- **FC:** a push token expires → the device record is refreshed on next open.

**US-09 Delete my account**
As a user, I want to delete my account and data.
- **AC:**
  - Two-step confirmation.
  - Profile deleted ≤ 30 days.
  - Predictions anonymised and removed from boards.
  - A reminder that store subscriptions must be cancelled in the store.
- **EC:** an active subscription → warning + store link.
- **FC:** a deletion job failure is retried and alerted. The user is never left half-deleted without a support path.

**US-10 Admin handles an abandoned match**
As an operator, I want to resolve an abandoned match correctly and transparently.
- **AC:**
  - The HOLD item shows the options (resumed / replay / result stands / void).
  - A reason is required.
  - An impact preview is shown.
  - Settlement is re-run and audit-logged.
- **EC:** a later provider update conflicts with the admin decision → alert, and the admin decision prevails until reviewed.
- **FC:** the operator lacks the role → the action is refused and logged.

**US-11 Arabic experience**
As an Arabic-speaking fan, I want a natural RTL experience.
- **AC:**
  - Mirrored layout.
  - The home team sits on the right in AR.
  - Arabic club names.
  - Western digits by default, with an Eastern Arabic option.
- **EC:** mixed Arabic/English names render correctly; long names are truncated gracefully.
- **FC:** a missing translation key falls back to English and is logged in QA, never shown as a raw key.

**US-12 Fair play**
As a fair player, I want cheaters kept off the boards.
- **AC:**
  - Predictions are locked server-side.
  - Others' picks are hidden until the lock.
  - Suspicious accounts are quietly excluded from public boards pending review.
- **EC:** a genuine expert flagged by the statistical outlier check → manual review before any action.
- **FC:** attestation is unavailable on an old device → the account is flagged for risk scoring, not blocked.

## 7. Dependencies & open items
Counsel answers (Q-16, Q-17, Q-19, Q-20) · repository creation (Q-22) · name vote + trademark (D-039) · V5 data trial · company registration and developer accounts.

## 8. Testing
Covered in Part 26 (testing strategy).

## 9. Sign-off
- [ ] Founder approves this PRD as the MVP scope.
- [ ] Changes after sign-off go through the Decision Log.

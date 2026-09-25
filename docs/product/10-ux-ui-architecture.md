# PART 10 — UX/UI Architecture

> Status: Draft v0.1 · 2026-09-25
> Principle: **a new user makes their first prediction in under 60 seconds.** Arabic RTL is designed first, and English is its mirror.
> Visual identity (colours, type, logo) comes in Part 18. This part covers structure and behaviour.

---

## 10.1 Information architecture

```
App
├── Splash
├── Onboarding: Language → Leagues → Clubs → First prediction → Sign-in → Notification permission
├── [Tab 1] Home
├── [Tab 2] Matches  ── Match details ── Prediction ── Confirmation
│                     └─ Live match     └─ Result breakdown
├── [Tab 3] Leaderboards (Global · Country · Competition · Club fans · Club vs club · Groups)
├── [Tab 4] Groups ── Group detail ── Invite · Settings (owner)
└── [Tab 5] Profile ── Stats · Achievements · Prediction history
         └── Settings ── Notifications · Language · Account · Subscription · Help · Privacy · Terms
Global: Notification inbox (bell icon in the header) · Paywall (modal) · Deep-link handler
```

**Navigation:** 5 bottom tabs, and the tab order mirrors in RTL. Predict is reachable from Home, from Matches, and from notifications (a deep link straight to the match).
**Friends, Challenges** (V1.1) will live inside the Groups tab, renamed "Social" at that point, so the tab count stays at 5.

## 10.2 Global states (apply to every screen)

| State | Pattern |
|---|---|
| Loading | Skeleton placeholders in the shape of the content (no full-screen spinners). Cached data is shown immediately with a subtle "updating" indicator |
| Empty | Illustration + one sentence + one primary action |
| Error (network) | Inline banner "You're offline. Showing saved data." with a retry button. Never block reading |
| Error (server) | "Something went wrong on our side" + retry + error ID for support |
| Prediction submit failure | **Never ambiguous.** The card shows one of: ✅ Saved (with server time) / ⏳ Sending / ❌ Not saved (reason + retry) |

## 10.3 Screen specifications

### Onboarding cluster

**S1 Splash**
- Purpose: brand moment and session check. Maximum 1.5 s.
- Components: logo, background preload of fixtures.
- Navigation: → Home (signed in) / → S2 (new user).
- Error: if configuration fails to load, continue with cached/default config.

**S2 Language**
- Purpose: choose Arabic or English. The default follows the device locale.
- Components: two large buttons (العربية / English).
- User actions: select → S3.

**S3 League selection**
- Purpose: personalisation.
- Components: grid of the 8 competitions with generated badges (D-006). Multi-select, and SPL + UAE PL are pre-ranked first in KSA/UAE.
- User actions: select ≥ 1 → Continue.
- Empty: n/a.
- Error: retry the list load.

**S4 Club selection**
- Purpose: favourite clubs, with one primary (feeds the club boards).
- Components: search, clubs grouped by selected league, star to mark the primary. An info tip explains: "Your primary club is the fan base you compete for. You can change it once per season."
- User actions: select ≥ 1, star 1 → Continue.
- Edge case: selecting one club automatically makes it the primary.

**S5 First prediction**
- Purpose: the "aha" moment before sign-up.
- Components: the next upcoming match of the primary club (fallback: the biggest upcoming match), the prediction stepper, and a lock countdown.
- User actions: set score → "Save prediction" → S6.
- Empty: no upcoming fixtures (off-season) → show a popular match from any competition, or skip to S6.

**S6 Sign-in**
- Purpose: account creation (Apple, Google, email). The pending prediction is held locally and submitted right after authentication.
- Components: provider buttons, a date-of-birth age gate (16+, D-014), and terms/privacy links.
- Error: if sign-in completes **after** the match locks, the app says "Kick-off passed, so this one couldn't be saved. Try the next match →". Under-16 → a polite block screen.

**S7 Notification permission (pre-prompt)**
- Purpose: explain the value before the OS dialog appears.
- Components: "Get a reminder before kick-off and your result at full time" + Allow / Not now.
- Navigation: → Home.

### Core cluster

**S8 Home** (personalised)
Section order (top to bottom), chosen by relevance:
1. **Primary club next match** card: prediction status ("Not predicted · locks in 2h 14m" or "You predicted 2–1").
2. **Needs your prediction**: open matches from followed leagues that lock within 48 h, with a "Predict all (7)" button to bulk mode.
3. **Live now** (when relevant): live score + "your potential points".
4. **Your week**: points this week, global rank, club-fans rank, streak.
5. **Club vs club** mini-table: your club's position and the gap to #1.
6. **Your groups** preview: top 3 of your most active group, with an invite button.
7. **Recent results**: last settled predictions with points.
8. **Recommended big matches** (derbies, UCL) outside the user's leagues.

- Empty (new user, off-season): "The season kicks off on {date}. Invite your group now" + invite button.
- Loading: skeleton cards.
- Error: cached home with an offline banner.
- *Friends activity and trending predictions come in V1.1 (they need the friends graph and the community-distribution flag).*

**S9 Matches (fixtures)**
- Purpose: browse and predict.
- Components: date strip (today ±7 days, jump to a date), competition filter chips (followed first), match rows: teams, kick-off in local time, prediction status chip (Open / Predicted / Locked / Live / +5).
- User actions: tap a row → S10. "Predict all" enters bulk mode (inline steppers on each row + Save all).
- Empty: "No matches on this day" + next match date.
- Error: retry.

**S10 Match details / Prediction**
Layout (RTL example: the home team sits on the right in Arabic):
```
   Al Hilal            vs          Al Ittihad
  [HIL badge]                      [ITT badge]
  Sat 26 Sep · 21:00 (your time) · Kingdom Arena
  Locks in 02:14:36  (server-synced)

     [-]  2  [+]                 [-]  1  [+]

           [ SAVE PREDICTION ]
  Scored on the 90-minute result · Exact 5 · GD 3 · Result 2
```
- Info sections below the fold (free): league position, last 5 form (W/D/L chips), head-to-head summary (last 5 meetings), goals scored/conceded per match. **Pro:** home/away splits, goal trends.
- Community distribution: **hidden until the user has saved a prediction** (or after lock), then shown as "Hilal 52% · Draw 28% · Ittihad 20%". This avoids herding (behind the `community_distribution` flag).
- Stepper: 0–15 per team. Long-press increments fast. There is no default score (both show "–") until the user taps, so nobody submits 0–0 by accident.
- Actions: save/update (unlimited edits before lock), share prediction (after saving).
- States: Locked (the steppers are replaced with "Your prediction: 2–1 · locked at 21:00:00"). Void ("Match postponed. Your prediction is kept and will reopen when a new date is set"). Error (❌ Not saved + reason).

**S11 Prediction confirmation**
- Inline, not a separate page: a green check + "Saved at 18:46:02 (server time)". Tapping it shows a receipt with the revision history ("changed 1–1 → 2–1 at 18:46").
- Follow-up actions: "Predict next match →" / "Share".

**S12 Live match** (MVP: basic card behind the `live_match_card` flag)
- Components: live score, match clock, your locked prediction, **"If it ends like this: +3"**, status of each scoring tier (exact ✗, GD ✓, result ✓).
- V2 adds events (goals, cards, substitutions), stats, and live leaderboard movement.
- Refresh: poll every 60 s while open (cost control, Part 13). Pull-to-refresh.
- Error: "Live data delayed", with the last update time shown.

**S13 Result breakdown**
- Purpose: explain the points and celebrate.
- Components: final (90-min) score, your prediction, points with the rule that applied ("Correct result + goal difference = 3"), rank change, club contribution, badges unlocked, share card.
- Correction state: "Result corrected on {date}. Points updated from 5 → 3" with an explanation.

**S14 Leaderboards**
- Components: scope selector (Global · Country · Competition · My club fans · Club vs club · Groups), period toggle (Week · Season; Month in V1.1), rows as defined in Part 8 §8.5, the user's own row pinned, and a percentile badge.
- Actions: tap a user → public profile (V1.1); tap a club → the club's fan board.
- Empty: "Not enough fans yet (12/30). Invite fans of your club" (club vs club threshold).
- Loading: skeleton rows.

### Social cluster

**S15 Groups list**
- Components: my groups (rank in each), "Create group", "Join with code".
- Empty: "Play with your friends. Create a group and share it on WhatsApp" (primary CTA).

**S16 Group detail**
- Components: group board (week/season), members, invite button (link + code + share sheet), owner settings (rename, remove member, custom scoring for Pro, regenerate the invite code).
- Error: invite link expired or invalid → "Ask your friend for a new link".
- Edge case: the owner leaves → ownership passes to the longest-standing member.

**S17 Achievements**
- Components: grid by category, locked badges show their criteria and progress bars ("7/10 matches").
- Empty: n/a (always shows locked badges).

**S18 Notification inbox**
- Components: a list of past notifications (results, invites, rank moves), tap → deep link.
- Empty: "No notifications yet".

*Friends, Challenges and head-to-head screens follow in V1.1.*

### Account cluster

**S19 Profile**
- Components: photo/initials, username, country flag, primary club badge, tier badge (Plus/Pro), level + XP bar, stats (total, correct, exact, accuracy %, current and longest streak), ranks (global, country, club), achievements row, prediction history link.

**S20 Statistics**
- Free users see basic totals. Plus/Pro see accuracy by league/club, trend charts, best and worst teams to predict.
- Free users get a blurred preview of **their own** data plus an upgrade prompt.

**S21 Prediction history**
- Filterable list (competition, result). Free: last 30 days; paid: everything.

**S22 Subscription (paywall + management)**
- Components: Free/Plus/Pro comparison, monthly/annual toggle ("2 months free"), trial terms, required disclosures (Part 9 §9.8), restore purchases, manage subscription (deep link to the store).
- Error: purchase failed or pending → clear message, no double charge. Entitlements refresh from the server.

**S23 Settings**
- Language, time zone (automatic + override), notifications (S23a), account (email, linked sign-ins, delete account, export data), subscription, help, privacy, terms, about (with the "not affiliated" disclaimer, D-006).

**S23a Notification settings**
- Per-category toggles (§10.6), quiet hours, "only my clubs" option (advanced rules are Plus).

**S24 Help**
- FAQ: scoring, locks, postponements, ties. Contact support (email form, which attaches app version and user ID).

**S25 Privacy / S26 Terms**
- In-app web views of the hosted legal pages, in AR and EN (counsel-drafted, D-017).

**S27 Delete account**
- Explains what gets deleted and what is kept (anonymised aggregates, legally required records). Two-step confirmation. It **reminds the user that deleting the account does not cancel a store subscription**, and links to the store.

## 10.4 RTL & localisation rules
- Use directional-aware layout everywhere (start/end, not left/right). Icons with direction (arrows, back) mirror; logos, clocks and media controls don't.
- **Score display:** in RTL the home team sits on the right, and the score reads naturally right to left ("2 – 1" with home = 2 on the right). This rule is fixed per locale and tested with native users.
- Numerals: Western Arabic digits (0–9) by default in both languages (the common choice in GCC sports apps **[validate with users]**), with an Eastern Arabic (٠–٩) option in settings.
- Dates: Gregorian by default, with an optional Hijri display. Kick-off times are always shown in the device time zone, with the zone label shown when it differs from the club's local time.
- Club and league names are stored in both languages in the database (not machine-translated at runtime).
- Arabic font with good numeral legibility at small sizes (Part 18).

## 10.5 Accessibility
- Minimum touch target 44×44 pt. Steppers are large.
- Supports dynamic type up to 200% without truncating scores.
- Colour is never the only signal: result chips use W/D/L letters as well as colour.
- Screen-reader labels, e.g. "Al Hilal 2, Al Ittihad 1, your prediction, locked".
- Contrast AA minimum.

## 10.6 Notification design (feeds Parts 8 and 16)

| Category | Example | Default | Cap |
|---|---|---|---|
| Prediction reminder | "Al Hilal vs Al Ittihad kicks off in 2 h. Make your prediction" | **On**, only for followed clubs + matches the user hasn't predicted | 1 per matchday (batched: "3 matches lock soon") |
| Result | "Full time: 2–1. EXACT! +5" | **On** | Batched if > 3 results within 30 min |
| Rank movement | "You climbed 24 places in the Saudi Pro League board" | On (weekly summary only) | 1 per week |
| Group activity | "Ahmed joined your group 'Office League'" | On | 1 per day digest |
| Club vs club | "Al Hilal fans are #1 this week!" | Off (opt-in) | 1 per week |
| Challenges / H2H (V1.1) | "Ahmed challenged you: Real Madrid vs Barcelona" | On | – |
| Marketing / product news | – | **Off** (opt-in) | 2 per month |

Global rules: quiet hours (default 00:00–08:00 local, user-editable), a combined cap of about 3 pushes per day, no notifications during prayer-time windows **[optional feature; validate with users]**, and each push deep-links to the relevant screen.

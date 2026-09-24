# PART 3 — Target Users & Personas

> Status: Draft v0.1 · 2026-09-24
> Personas are **hypotheses** built from the Part 2 research and domain judgment. They are not survey data.
> The Phase 0 experiments (Part 2 §2.4) must confirm or correct them.

---

## 3.1 Segments

| Segment | Market | Priority | Role in the business |
|---|---|---|---|
| **S1: Rival club fans** | KSA/UAE, Arabic-speaking, 18–35 | **Primary** | Core players. They drive club leaderboards |
| **S2: Group organizers** | KSA/UAE, any age 22–45 | **Primary** | The growth engine. One organizer brings 5–30 people |
| **S3: Stats-minded fans** | KSA/UAE + expats | Secondary | Most likely buyers of the top paid tier |
| **S4: Casual social fans** | KSA/UAE | Secondary | Volume. They play mostly because their group plays |
| **S5: English-speaking expats** | UAE/KSA | Secondary | Bridges to the English market, follows European leagues |
| **S6 (future): Organizations** | Companies, universities, fan clubs | Future (B2B) | Paid private competitions |

## 3.2 Personas

### P1 — Faisal, "The Rival" (S1, primary)
- **Profile:** 23, university student in Riyadh. Arabic UI. Al Hilal fan who also follows Real Madrid.
- **Behaviour:** Lives in football group chats. Watches every Hilal match, and argues with Nassr-supporting friends constantly.
- **Goals:** Prove he knows football better than his friends, and see Hilal fans beat Nassr fans.
- **Frustrations:** Arguments with no record, English-only apps that ignore the Saudi league, betting-style apps he won't touch.
- **What wins him:** The club-vs-club leaderboard, shareable result cards, a weekly rank to defend.
- **Pays for:** Probably Plus, for a cosmetic club-pride profile frame and ad-free. Price-sensitive.
- **Quote (illustrative):** "Screenshot or it didn't happen. Now there's a table."

### P2 — Khalid, "The Organizer" (S2, primary)
- **Profile:** 38, operations manager in Dammam. Arabic UI. Al Ettifaq and Liverpool fan.
- **Behaviour:** Already runs a family and office prediction competition by hand, collecting picks in WhatsApp and totalling them in a notebook or spreadsheet.
- **Goals:** Zero admin, fair automatic scoring, everyone able to see the table.
- **Frustrations:** Chasing people for picks, disputes about late submissions, arithmetic errors.
- **What wins him:** Create a group in under a minute, share an invite to WhatsApp, automatic deadline enforcement, automatic reminders to members.
- **Pays for:** Pro, if it adds group-admin power (larger groups, custom group scoring, group statistics).
- **Why he matters:** Every Khalid brings his whole network. **Design the group flow for him.**

### P3 — Omar, "The Analyst" (S3)
- **Profile:** 34, engineer in Abu Dhabi. Switches between Arabic and English. Al Ain and Manchester City fan.
- **Behaviour:** Checks stats apps before every match and likes to understand why he was right or wrong.
- **Goals:** Improve his accuracy, see his strengths by league and team, climb the season ranking.
- **Frustrations:** Shallow stats in game apps, no personal performance history.
- **What wins him:** A personal analytics dashboard, accuracy by league/club, prediction history, pre-match insight depth.
- **Pays for:** Pro, annual. He is the highest-LTV persona.

### P4 — Noura, "The Social Player" (S4)
- **Profile:** 27, marketing specialist in Jeddah. Arabic UI. Casual Al Ittihad fan.
- **Behaviour:** Plays because her friends' or office group does. Predicts big matches, not every match.
- **Goals:** Take part without it taking much time, and enjoy the banter.
- **Frustrations:** Complex rules, too many notifications, feeling hopeless when far behind.
- **What wins her:** Predicting in about 10 seconds, simple scoring, weekly resets, gentle and controllable notifications.
- **Pays for:** Unlikely. She is valuable for group density and ad inventory (non-intrusive only).

### P5 — Arjun, "The Expat" (S5)
- **Profile:** 31, finance professional in Dubai. English UI. Manchester United fan.
- **Behaviour:** Follows the Premier League and UCL. Works in a multinational office in the UAE.
- **Goals:** An office league with colleagues who follow different leagues.
- **What wins him:** Mixed-league groups, an English UX of the same quality as the Arabic one, correct local kick-off times.
- **Pays for:** Plus or Pro. His willingness to pay is likely higher than P1's **[assumption]**.

## 3.3 Anti-personas (users we deliberately do not design for)

| Anti-persona | Why we exclude them | Product implication |
|---|---|---|
| Bettors looking for tips/odds | Legal, brand and app-store risk | No odds, no tipster content, no bookmaker links |
| Under-age users (below the minimum age, see Q-11) | Compliance | Age gate at sign-up |
| Multi-account farmers / bots | Destroy leaderboard integrity | Anti-cheat (Part 14) |
| Hardcore fantasy managers wanting squad depth | A different product | Don't add squad mechanics |

## 3.4 Persona × feature priority

| Feature | P1 Rival | P2 Organizer | P3 Analyst | P4 Social | P5 Expat |
|---|---|---|---|---|---|
| 10-second score prediction | ●●● | ●●● | ●●● | ●●● | ●●● |
| Club-vs-club leaderboard | ●●● | ●● | ●● | ● | ● |
| Private groups + WhatsApp invite | ●●● | ●●● | ●● | ●●● | ●●● |
| Fast settlement + result push | ●●● | ●●● | ●●● | ●● | ●● |
| Personal stats/analytics | ● | ● | ●●● | – | ●● |
| Shareable result cards | ●●● | ●● | ● | ●● | ● |
| Notification controls | ●● | ●● | ●● | ●●● | ●● |
| Arabic RTL quality | ●●● | ●●● | ●● | ●●● | – |

## 3.5 Key design principles derived from personas
1. **Make the first prediction within 60 seconds of install.** Onboarding is league → club → first match. Account creation can come just after the first pick, but the pick is only submitted once the user is signed in (see Part 10).
2. **The organizer is a first-class user.** Group creation and invites must be flawless.
3. **Nobody should feel hopeless.** Weekly boards, personal bests, and club contribution ("you earned 12 points for Hilal fans").
4. **Arabic is designed first, not translated.** Test layouts in RTL first.
5. **Respect attention.** Notifications default to only the essentials, and every category can be switched off.

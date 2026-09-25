# PART 8 — Gamification System

> Status: Draft v0.1 · 2026-09-24
> Goal: competitive and habit-forming **in a healthy way**. Reward knowledge and participation, never compulsion or spending.

---

## 8.1 Currencies, clearly separated (Points, XP, and Coins from V1.1, see §8.8)

| | **Points** | **XP** |
|---|---|---|
| Measures | Prediction **accuracy** | **Participation** and progress |
| Earned by | Settled predictions only (Part 7) | Predicting, streaks, achievements, challenges, inviting friends |
| Drives | **All leaderboards** | **Level**, badges, cosmetics unlocks |
| Can be bought / boosted? | **Never** | **Never** (no XP boosts for sale either, to protect the perception of fairness) |
| Resets | Weekly/monthly boards reset; season total per season | Never (lifetime) |

Why separate them: a loyal but average predictor still progresses (level, badges) without distorting the competitive boards.

## 8.2 XP & levels

| Action | XP |
|---|---:|
| Submit a prediction | 10 |
| Predict every fixture of your primary club's competition in a matchweek | +30 |
| Correct result | +5 |
| Exact score | +20 |
| Unlock an achievement | 25–250 (by rarity) |
| Invited friend joins **and makes their first prediction** | +50 (max 10 per month) |
| Complete a weekly challenge (V1.1) | 50–150 |

**Level curve:** `XP needed for level n = 100 × n^1.5` (rounded). Early levels come fast (L2 at ~280 XP, in the first week), and later ones need a season of play. Levels unlock only cosmetic items: profile frames and titles such as "Analyst" or "Legend". These are free and separate from the Plus/Pro cosmetics.

Invite XP is only awarded after the invitee's first prediction, and is capped, so bogus accounts can't farm it.

## 8.3 Streaks (with burnout protection)

| Streak | Definition | Protection |
|---|---|---|
| **Participation streak** (primary) | Consecutive matchweeks with ≥ 1 prediction in any followed competition | International breaks and off-weeks with no fixtures are skipped automatically, not counted as missed |
| **Result streak** | Consecutive settled predictions with the correct result | Void matches neither break nor extend it |
| **Exact streak** | Consecutive exact scores (rare, used for achievements) | – |

**No daily streaks.** Football is weekly, and daily streaks push compulsive checking with no football reason behind it.
**Streak freeze:** everyone gets one automatic freeze per month. It is not sold, because selling it would create pay-to-protect pressure.

## 8.4 Achievements (badges)

Tiers: Bronze / Silver / Gold / Legendary. **MVP ships 10 (★).** The rest arrive in V1.1–V2 once data shows which ones motivate users.

### Onboarding & participation
| Badge | Criteria | Tier |
|---|---|---|
| ★ **Kick-off** | First prediction | Bronze |
| ★ **Matchweek Complete** | Predict every fixture in a competition's matchweek | Bronze |
| ★ **Regular** | 4-matchweek participation streak | Silver |
| **Season Ticket** | 30-matchweek participation streak | Gold |
| **Centurion** | 100 predictions | Silver |
| **Thousand Club** | 1,000 predictions | Legendary |

### Accuracy
| Badge | Criteria | Tier |
|---|---|---|
| ★ **Perfect Prediction** | First exact score | Bronze |
| ★ **Hat-Trick** | 3 exact scores in a row | Gold |
| ★ **10 in a Row** | 10-result streak | Gold |
| **Perfect Weekend** | Every result correct in a matchweek (min 5 predictions) | Gold |
| **Upset Artist** | Correct result predicted by < 15% of the community, 5 times | Silver |
| **Draw Whisperer** | 5 correct draw predictions in a season | Silver |
| **Sniper** | 25 exact scores in a season | Legendary |

### League & club mastery
| Badge | Criteria | Tier |
|---|---|---|
| **[League] Expert** (e.g. Saudi Pro League Expert) | ≥ 55% correct results over ≥ 50 predictions in that league during a season | Gold |
| **[League] Master** | Top 1% of the league board at season end | Legendary |
| ★ **True Fan** | Predict 10 matches of your primary club | Bronze |
| **Club Oracle** | 5 exact scores in your primary club's matches | Silver |
| **Heart over Head** | Predicted your club to lose, and it did (a humorous badge) | Bronze |
| **Derby Day** | Exact score in a designated derby (e.g. Riyadh derby, El Clásico) | Gold |

The 55% threshold is an assumption to calibrate with real data; the target is roughly the top 20% of players.

### Social
| Badge | Criteria | Tier |
|---|---|---|
| ★ **Squad Builder** | Create a group with ≥ 5 members | Silver |
| ★ **Recruiter** | 3 invited friends make their first prediction | Silver |
| **Group Champion** | Win a group's weekly board | Bronze |
| **Season Champion** | Win a group's season board (min 5 members) | Gold |
| **Club Hero** | Your points put your club at #1 in the weekly club-vs-club board (top 10% contributor) | Gold |

### Personal records (profile, not badges)
Best matchweek points, longest result streak, most exact scores in a matchweek, highest global rank, best league accuracy.

## 8.5 Leaderboards

**Dimensions:** scope × period.

| Scope | Week | Month | Season | MVP |
|---|:-:|:-:|:-:|:-:|
| Global | ● | V1.1 | ● | ● |
| Country (from profile) | ● | V1.1 | ● | ● |
| Competition (e.g. SPL only) | ● | V1.1 | ● | ● |
| Club fans (users whose primary club = X) | ● | V1.1 | ● | ● |
| **Club vs club** | ● | V1.1 | ● | ● |
| Private group | ● | ● | ● | ● |
| Friends | – | – | – | V1.1 |

The game week runs **Tuesday 04:00 to the next Tuesday 03:59, Asia/Riyadh time**, for everyone. Each match belongs to the week containing its kick-off. Starting on Tuesday keeps Monday-night European matches in the same week as the weekend round, and starts the new week with the Champions League (Tue/Wed). The reset time is the same for all users. The season follows the competition's season.

Each row shows: rank, movement (▲▼ since last settlement), name, club badge, points, exact scores, accuracy %, current streak. The user's own row is pinned at the bottom if they're off-screen. Tie rules are in Part 7 §7.7.

### Club-vs-club formula (Q-15)
Summing points would just reward the biggest fan base, so the ranking uses a quality measure with a minimum sample size:

```
club_score(period) = mean over the club's active fans of that fan's points in the period
active fan  = primary club = X, account ≥ 7 days old, ≥ 3 settled predictions in the period
eligible    = club has ≥ 30 active fans in the period (otherwise shown as "not enough fans yet")
```
- The mean rewards quality, and the minimum sample stops a few experts from gaming it.
- The UI shows each fan their **contribution**: "You scored 14, club average 9.2. You lifted Al Hilal."
- Anti-hopping rule (Part 7 §7.8): the primary club can change once per season.
- Initially the board covers the clubs of the 8 MVP competitions. A cross-league view such as "Top fan bases in the Arab world" comes later.
- The thresholds (30 fans, 3 predictions) are assumptions to tune during the beta.

### Fairness & motivation
- **Weekly reset** gives everyone a fresh chance each week.
- **"Your percentile"** ("Top 18%") is shown when the absolute rank is discouraging.
- **Divisions** (V2): groups of about 50 players with promotion and relegation each month, so everyone competes at their level.

## 8.6 Challenges (V1.1)

| Type | Examples | Reward |
|---|---|---|
| Weekly | "Predict 5 matches from 2 competitions", "Get 2 exact scores" | XP |
| Monthly | "Finish in the top 25% of your club's fans", "Predict every El Clásico/derby this month" | XP + badge |
| Seasonal | "Premier League Expert", "Complete 30 matchweeks" | Legendary badge + cosmetic |
| Event | AFC Asian Cup 2027 tournament mode, UCL knockouts | Event badge |
| Sponsored (V2) | Brand-named challenge with **XP/badge rewards only** | No prizes without legal sign-off (D-001) |

## 8.8 Fan Store & Coins (founder idea, 2026-09-25; target V1.1)

**Idea:** users spend what they earn from predictions in an in-app store (avatars and similar items). Some items require a subscription.

**Design adjustment: a third, spendable currency called "Coins".** Leaderboard **Points** are never spent.
If users spent Points, buying an avatar would lower their rank. That would punish shoppers, and the leaderboard would stop measuring prediction skill. So:

| | Points | XP | **Coins** |
|---|---|---|---|
| Purpose | Rank (accuracy) | Level (participation) | **Spending** in the Fan Store |
| Earned from | Settled predictions | Activity, achievements | **Mirrors earned points 1:1** + achievement/challenge bonuses |
| Spent? | Never | Never | **Yes**, on cosmetic items |
| Bought with money? | Never | Never | **Never** (see legal notes) |

For example, an exact score gives +5 Points (rank) and +5 Coins (wallet). Buying an item removes Coins only, and the rank is unchanged.

### Store catalogue (cosmetic only)
| Category | Examples | Notes |
|---|---|---|
| Avatars | Stylised fan characters, club-colour outfits, keeper/striker poses | **Generic designs**: no real kits, crests or player likenesses (D-006) |
| Avatar accessories | Scarves, flags, face paint in club colours, trophies | Club colours only, no logos |
| Profile frames & banners | Stadium, derby-day, Ramadan/Eid seasonal frames | Seasonal items create returning demand |
| Prediction card themes | Styles for shared result cards | Adds viral value |
| Titles | "Derby Specialist", "Night Owl" | Text only, cheap to produce |

### Access rules
| Item type | Free | Plus | Pro |
|---|:-:|:-:|:-:|
| Standard items (bought with Coins) | ✅ | ✅ | ✅ |
| **Plus-exclusive items** (bought with Coins, need Plus) | 🔒 visible, "Plus required" | ✅ | ✅ |
| **Pro-exclusive items** (bought with Coins, need Pro) | 🔒 | 🔒 | ✅ |
| Monthly free subscriber item | – | 1 item/month | 2 items/month |
| Coins earned per point | 1× | **1×** | **1×** |

- **No coin multipliers for subscribers.** Paid users get *access* to exclusive items, not a faster economy. This keeps D-002's fairness spirit.
- If a subscription lapses, exclusive items already bought **stay owned** but can't be **equipped** until the user resubscribes. They are never deleted. The rule is stated clearly before purchase.
- Locked items are visible in the store. That makes them a natural, non-pushy upgrade prompt (Part 9 §9.6).

### Economy rules
- **Prices:** standard items 20–150 Coins, exclusive items 100–400 Coins **[tune in beta]**. An active player earning ~20–30 points a week can afford a standard item every 1–3 weeks.
- **Sinks & freshness:** seasonal and limited-time items. There is no resale or trading.
- **Corrections:** if a result correction lowers points, Coins are debited too. The balance may go negative, which blocks purchases until it's positive again. This is shown transparently.
- **Anti-farming:** Coins come only from settled predictions and achievements, which the anti-cheat rules already protect (Part 7 §7.8). Coins are non-transferable, so multi-accounts gain nothing by feeding a main account.

### ⚠️ Legal & app-store guardrails (for counsel, D-017)
1. **Coins can never be bought with real money, exchanged for cash, or transferred between users.** This keeps them a pure game reward and avoids virtual-currency and gambling characterisation.
2. **No random rewards (loot boxes, mystery packs).** Random paid rewards are regulated in several markets, and Apple requires odds disclosure. We sell only fixed, known items.
3. Subscriber-exclusive items are a normal subscription benefit, delivered through the entitlements system (Part 6 §6.4).
4. If we ever want to **sell Coins** (a possible V2 revenue stream), it goes through Apple/Google IAP only, gets a separate counsel review, and must never let purchased Coins affect Points or rank.

### Build cost & timing
- **Recommended timing: V1.1**, right after the MVP proves the weekly habit. The MVP already records the Coins ledger from day one, so early players keep their earnings when the store opens (and get a "founding fan" item).
- **Main cost is art, not code.** About 30–40 launch items. Budget options:
  - a modular avatar system (base body + colour-tinted layers), where one set of artwork produces hundreds of club-colour combinations
  - a single freelance illustrator
- Code: store screen, inventory, equip, and the coin ledger. That is roughly 2–3 weeks for one developer **[estimate]**.

## 8.7 Healthy-engagement guardrails
- Notifications are capped (see Part 10 §10.6): at most ~1 reminder per matchday, and quiet hours are respected.
- No loot boxes, random rewards for money, or paid streak protection.
- No dark patterns. Cancelling a subscription is as easy as starting one.
- Rewards come from football events, not from time spent in the app.

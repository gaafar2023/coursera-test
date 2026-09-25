# GOOOAL Test League Kit (Phase 0)

> Goal: in **4 weeks**, at almost **zero cost**, find out whether fans will predict every week, invite friends, and care about the club-vs-club table, **before** we spend money building the app. (Part 2 §2.4, experiment V3; approved Q-05.)
> Scoring spreadsheet: [`GOOOAL-Test-League-Scorer.xlsx`](./GOOOAL-Test-League-Scorer.xlsx)

---

## 1. What you need (all free)
- A **WhatsApp group or Telegram group**. Telegram allows larger groups and polls; WhatsApp is where most people already are. You can run one of each.
- A **Google Form** (forms.google.com), one per week.
- The **scoring spreadsheet** above. It opens in Excel and in Google Sheets: File → Import.
- About **1 hour per week** of your time.

## 2. Setup (once)
1. Open the spreadsheet → **Clubs** tab: check the club list and add any missing clubs.
2. Delete the green **example rows** in Players, Matches and Predictions.
3. Create the group and post the **welcome message** (§5.1).
4. Aim for **30–100 players** from at least **3–4 different club fan bases**, so the club-vs-club table is meaningful.

## 3. Weekly routine

| Day (typical) | You do | Time |
|---|---|---|
| **Tuesday** | Pick **6–10 matches** for the week (always include 2–3 Saudi Pro League and 1–2 UAE Pro League matches, plus the biggest European ones). Create the week's Google Form (§4). Post the **new round message** (§5.2) | 20 min |
| **Before the first kick-off** | Post a **reminder** (§5.3). The spreadsheet ignores any pick made after that match's kick-off (it compares the form timestamp with the kick-off time) | 2 min |
| **After each match** | Type the **90-minute score** in the Matches tab. Postponed or cancelled → Status = Void | 1 min per match |
| **Monday** | Download the form responses (Responses → Download CSV) and paste them into the Predictions tab. Set the week in **Leaderboard!B2**. Screenshot the **week table**, **season table** and **club-vs-club table**, then post the **results message** (§5.4) | 20 min |
| **Every week** | Fill in the **Tracking** tab: new invites, and what people said | 5 min |

## 4. The Google Form (build it this way so pasting is easy)
1. **Question 1:** "Your name" (short answer, required). Ask players to use **the same name every week**, exactly as in the Players tab.
2. Then **for each match, in the same order as the Matches tab (Match #1, #2, …)**, add two questions:
   - "Al Hilal goals" (short answer, response validation: number, between 0 and 15)
   - "Al Nassr goals" (same validation)
3. Settings: turn **off** "limit to 1 response" if people don't have Google accounts; turn **on** "Collect email" only if needed. **Close the form** (Responses → "Accepting responses" off) at the first kick-off, or leave it open, since the spreadsheet removes late picks per match anyway.
4. When pasting into **Predictions**: column A = week number (type it), B = name, C = the form's timestamp, then D onwards = the goals in order (M1 home, M1 away, M2 home, …).

Tip: after week 1, duplicate the previous week's form (⋮ → Make a copy) and just change the team names.

## 5. Ready-to-send messages

### 5.1 Welcome message
**العربية**
> ⚽ أهلاً بكم في دوري توقعات **GOOOAL (جووول)** التجريبي!
> كل أسبوع ننشر مباريات مختارة من الدوري السعودي والإماراتي وأقوى الدوريات الأوروبية، وتتوقعون النتيجة.
> 🏆 النقاط: النتيجة بالضبط = 5 · الفائز وفارق الأهداف = 3 · الفائز فقط = 2
> 🔵🟡 والأهم: أي جمهور يتوقع أفضل؟ جدول **النادي ضد النادي** كل أسبوع!
> ✅ مجاني بالكامل، بدون أي أموال أو جوائز. للمتعة والتحدي فقط.
> اكتب اسمك وناديك المفضل هنا للتسجيل 👇

**English**
> ⚽ Welcome to the **GOOOAL** test prediction league!
> Every week we post selected matches from the Saudi Pro League, UAE Pro League and Europe's top leagues. You predict the scores.
> 🏆 Points: exact score = 5 · right winner + goal difference = 3 · right winner = 2
> 🔵🟡 The big one: which fan base predicts best? A **Club vs Club** table every week!
> ✅ 100% free: no money, no prizes. Just fun and bragging rights.
> Reply with your name and favourite club to join 👇

### 5.2 New round
**العربية**
> 📋 **الجولة {رقم}** جاهزة! {عدد} مباريات هذا الأسبوع، أبرزها: {المباراة الكبرى}
> توقعاتك هنا 👉 {رابط النموذج}
> ⏰ التوقع يُقفل عند انطلاق كل مباراة.

**English**
> 📋 **Round {n}** is open! {count} matches this week, including {big match}.
> Predict here 👉 {form link}
> ⏰ Each match locks at kick-off.

### 5.3 Reminder
> ⏰ باقي {ساعتين} على أول مباراة ({المباراة}). لا تنسَ توقعاتك 👉 {الرابط}
> ⏰ {2 hours} until the first kick-off ({match}). Get your predictions in 👉 {link}

### 5.4 Results
**العربية**
> 📊 **نتائج الجولة {رقم}**
> 🥇 {الأول} ({النقاط} نقطة) · 🥈 {الثاني} · 🥉 {الثالث}
> 🎯 التوقعات الدقيقة: {الأسماء}
> 🔥 **النادي ضد النادي:** جمهور {النادي} في الصدارة بمعدل {X} نقطة للمشجع!
> الجولة القادمة تفتح الثلاثاء. ادعُ أصحابك وارفع معدل ناديك 💪

**English**
> 📊 **Round {n} results**
> 🥇 {1st} ({pts} pts) · 🥈 {2nd} · 🥉 {3rd}
> 🎯 Exact scores: {names}
> 🔥 **Club vs Club:** {club} fans lead with {X} points per fan!
> Next round opens Tuesday. Invite your friends and lift your club's average 💪

### 5.5 Week-3 feedback survey (a separate short Google Form)
1. How likely are you to keep playing? (0–10)
2. What do you enjoy most? (weekly table / club vs club / beating friends / other)
3. What's annoying or missing?
4. Would you want your own private league with your friends, family or office? (yes/no)
5. **"If the app offered a 'Pro stats pack' (your accuracy by league and team, deeper match stats) for about {X} SAR/month, would you consider it?"** (yes / maybe / no)
   This is only a *question*: **no payment is taken** (fake-door test, Part 9 §9.4). Try 2–3 different prices across different groups.
6. Pricing check (Van Westendorp): at what monthly price would Pro feel: too cheap to trust / a bargain / getting expensive / too expensive?

### 5.6 Name vote (run it in week 2 or 3, when the group is active)
Post as a **Telegram poll** or a **WhatsApp poll** (single choice):

**العربية**
> 🗳️ ساعدونا نختار اسم التطبيق الرسمي! أي اسم يعجبكم أكثر؟
> 1️⃣ **FootGooal** (فوت جووول)
> 2️⃣ **Mudarraj** (مدرّج): المدرّج، مكان الجماهير
> 3️⃣ **KooraCall** (كورة كول): قول توقعك في الكورة

**English**
> 🗳️ Help us choose the app's official name! Which one do you like most?
> 1️⃣ **FootGooal**
> 2️⃣ **Mudarraj**: "the stands", where the fans are
> 3️⃣ **KooraCall**: make your call on the koora

Also ask in the week-3 survey: "Which name is easiest to remember?" and "Which name would you search for in the app store?" Record the results in the Tracking tab notes. The winner then goes to the lawyer for a trademark check (Part 18 §18.3).

## 6. What to watch (the Tracking tab does the maths)

| Signal | Target | Where |
|---|---|---|
| Week-4 retention (players from week 1 still predicting in week 4) | **≥ 40%** | Tracking!D7 + verdict in B19 |
| Players invite friends or ask for their own groups **without being pushed** | Several unprompted requests | Your notes in Tracking |
| Club-vs-club table gets reactions and banter | Clearly visible | Your notes |
| "Would consider Pro" in the survey | **≥ 5%** | Survey results |

**Decision after 4 weeks:**
- Targets met → we build the MVP with confidence.
- Retention low → we study why before building (too many matches? boring? wrong time?), adjust, and run 2 more weeks.

## 7. Rules to keep it compliant (No-Gambling Charter, Part 17)
- **No money, no entry fees, no prizes, not even small ones** such as vouchers, jerseys or phone credit. Recognition only: shout-outs and titles.
- Don't use betting words (bet, odds, jackpot, رهان، مراهنة). Use "predict / توقّع" and "points / نقاط".
- Don't post betting odds or tipster links in the group, and remove any that others post.
- If influencers promote the league **as paid promotion**, check that they hold a **Mawthooq** licence (KSA) or a **UAE Advertiser Permit** (Part 17 §17.4).
- Collect only names/nicknames and clubs. No phone lists or personal data in the spreadsheet beyond what's needed.

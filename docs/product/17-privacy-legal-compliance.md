# PART 17 — Privacy, Legal & App-Store Compliance

> Status: Draft v0.1 · 2026-09-25
> **This is not legal advice.** It is a compliance map for the founder's KSA and UAE counsel (D-017) to review, correct and sign off.
> Items marked **[counsel]** need a written legal opinion. Items marked **[verify]** need the current official text checked.

---

## 17.1 No-Gambling Charter (founder principle, permanent — D-001)

> **[App Name] is a free football prediction game for fun, knowledge and bragging rights. It is not, and will never
> become, a gambling, betting, lottery or prize-competition product.**

### The three elements of gambling, and how we remove each one
Gambling laws generally look for three elements together: **consideration** (paying or staking something of value to play), **chance** (an uncertain outcome) and a **prize** (winning something of value). Football results are uncertain, so we make sure the other two elements are **never** present:

| Element | Our rule |
|---|---|
| **Consideration** | Playing (predicting, joining leaderboards and groups) is **always free**. Subscriptions buy only comfort, statistics and cosmetics, never entry into any competition, extra chances or better odds of ranking |
| **Prize** | **Nothing of monetary value can be won.** No cash, vouchers, gift cards, merchandise, tickets, phone credit or crypto. Points, XP, Coins, badges and cosmetics have **no cash value**, can't be exchanged, sold or transferred, and can't be bought |
| **Chance for value** | No random paid rewards: no loot boxes, spins, mystery packs or draws |

### Permanent rules
1. No wagering or staking of anything: money, points or Coins.
2. No paid entry to any competition, group, challenge or leaderboard.
3. No cash or real-world prizes, including sponsor-funded prizes and "free entry" prize draws.
4. No purchase of Points, Coins, XP or streak protection.
5. No betting odds, bookmaker data, tipster content or betting-affiliate links, anywhere.
6. No gambling, betting, lottery or casino advertising (enforced through the ad-network category blocklist).
7. No gambling vocabulary in the product, store listing or marketing (§17.2).
8. Community prediction percentages are shown as "fan opinion", never as odds.
9. Every new feature passes the **gambling-risk gate** (§17.3) before it is built.

These rules are written into the Terms of Service, so users, partners, app stores and regulators can see our position.

## 17.2 Vocabulary policy (EN / AR)

| ❌ Never use | ✅ Use instead |
|---|---|
| bet, wager, stake, punt, gamble | predict, pick, call |
| odds, price, line | fan opinion %, form, stats |
| jackpot, payout, cash out, winnings | points, rewards, badges |
| tipster, tips, bookie, banker, accumulator | predictions, insights, Star Match |
| رهان، مراهنة، قمار، يانصيب، ميسر | توقّع، توقعات |
| جائزة نقدية، ربح مالي، سحب | نقاط، شارات، مكافآت داخل اللعبة |
| احتمالات المراهنة | رأي الجماهير، إحصائيات |

Applies to app strings, push notifications, the store listing and keywords (ASO), social posts, influencer briefs and sponsor materials. It is checked with an automated lint on the translation files (ARB) and a manual copy review before each release.

## 17.3 Gambling-risk gate (for every new feature)

Before any feature enters a sprint, answer these questions:
1. Does the user pay, stake or risk anything of value to take part? → must be **No**
2. Can the user receive anything with monetary or real-world value? → must be **No**
3. Is any reward random and linked to payment? → must be **No**
4. Does it use betting data, odds or betting language? → must be **No**
5. Could it reasonably look like betting to a regulator, app reviewer or parent? → if **Maybe**, send it to counsel

**Any "Yes" blocks the feature.** The answers are recorded in the decision log.

## 17.4 Compliance register

### Kingdom of Saudi Arabia
| Area | Law / authority | How it applies to us | Our measures | Status |
|---|---|---|---|---|
| Gambling | Gambling is prohibited under Saudi law | We must not be characterised as gambling | Charter §17.1 | **[counsel]** confirm in writing that a free, no-prize prediction game falls outside the prohibition |
| Prize competitions | Ministry of Commerce rules under the Anti-Commercial Fraud Law: commercial competitions need a licence, must not require purchase, and must not violate Sharia or customs. Violations can bring up to 3 years in prison and SR 1M in fines ([Arab News](https://www.arabnews.com/node/2571612/amp), [SPA](https://spa.gov.sa/en/N2070645)) | Would apply if we ran prize competitions | **We run none** (Charter rule 3) | Excluded by design |
| Personal data | **PDPL** + Transfer Regulations (SDAIA). Cross-border transfers need safeguards and documentation ([King & Spalding](https://www.kslaw.com/news-and-insights/international-personal-data-transfers-under-saudi-arabias-data-protection-law)) | Saudi users' data | Data minimisation (Part 12), privacy notice in Arabic, consent where required, transfer safeguards or in-KSA hosting (Q-16), breach-notification process, records of processing | **[counsel]** Q-16, Q-17 |
| Media & advertising | General Authority for Media Regulation (GMedia, formerly GCAM). Influencer advertising requires a **Mawthooq** licence ([GMedia](https://gmedia.gov.sa/en/services/licensing-for-providing-advertising-content-through-social-media-platforms-trusted)) | Our influencer marketing (D-018) | Only work with influencers holding a valid Mawthooq licence, with the ad disclosed as paid | Process defined |
| E-commerce / consumer | E-Commerce Law and consumer rules **[verify]** | Subscriptions sold in-app | Clear Arabic pricing, renewal and cancellation terms; store-managed billing | **[counsel]** |
| Online content | Anti-Cyber Crime Law **[verify]** | User-generated usernames, group names, avatars | Moderation (§17.7) and a reporting tool | Designed |
| Business registration | Commercial registration (Saudi Business Center) **[counsel]** | Needed to trade, to sign data-provider contracts and to sponsor Mawthooq campaigns | – | **Q-19** |

### United Arab Emirates
| Area | Law / authority | How it applies to us | Our measures | Status |
|---|---|---|---|---|
| Commercial gaming | **GCGRA** regulates lottery, internet gaming, sports wagering and casinos. Federal Decree-Law No. 25/2025 in force since 1 June 2026 ([GCGRA](https://www.gcgra.gov.ae/en/)) | We must stay clearly outside "commercial gaming" | Charter §17.1 | **[counsel]** written confirmation that no licence is required |
| Personal data | **Federal Decree-Law No. 45/2021 (PDPL)**, in force since 2 Jan 2022. The status of the executive regulations is reported as unclear in public sources ([UAE Government portal](https://u.ae/en/about-the-uae/digital-uae/data/data-protection-laws), [Chambers 2026](https://practiceguides.chambers.com/practice-guides/data-protection-privacy-2026/uae/trends-and-developments)) | UAE users' data | Same privacy programme as the KSA one. If the company sits in a free zone (DIFC/ADGM), their own data laws apply instead **[counsel]** | **[counsel]** |
| Media & advertising | **UAE Media Council.** Influencers need an **Advertiser Permit** from 1 Feb 2026, with fines up to AED 500,000 ([Middle East Briefing](https://www.middleeastbriefing.com/news/uae-influencers-must-obtain-advertiser-permit-under-new-media-law/)). Media content standards apply | Influencer campaigns, app content | Verify permits before any campaign. Content follows national media standards (no content offending religion, the state or public morals) | Process defined |
| Consumer protection | Federal consumer protection law **[verify]** | Subscriptions | Same as KSA | **[counsel]** |
| Online content | Federal cybercrime law **[verify]** | User content | Moderation (§17.7) | Designed |

### Expansion markets (D-011)
Before each new country launches: a gambling/prize check, a data-protection check (e.g. Qatar, Bahrain, Oman, Kuwait, Egypt, Jordan and Morocco each have their own frameworks **[verify each]**), advertising and influencer rules, and local-language terms. **Nothing launches in a new country without counsel sign-off.**

### International baseline
- We build to **GDPR-grade** practices (access, export, deletion, minimisation, purpose limitation). EU residents may use the app, and this also covers most GCC requirements.

## 17.5 App Store & Google Play requirements

| Requirement | Apple | Google Play | Our design |
|---|---|---|---|
| Gambling / contests | Guideline 5.3 applies to contests, sweepstakes and real-money gaming | Real-Money Gambling, Games and Contests policy | **Not triggered**: no prizes, no paid entry (Charter). App review notes will say so explicitly |
| Age rating | Age-rating questionnaire | IARC questionnaire | Answer "no gambling / no simulated gambling". Expected rating: low/teen **[verify]**. Our own age gate is 16+ (D-014) |
| Account deletion | In-app account deletion required | In-app deletion + a web deletion link required | S27 screen + web form (Part 10) |
| Sign in with Apple | Required when offering other third-party logins **[verify guideline 4.8 wording]** | – | Included |
| Subscriptions | Auto-renew disclosures, restore purchases, all digital goods via IAP | Play Billing, subscription policy | Part 9 §9.8 |
| Privacy disclosures | Privacy "nutrition label" + privacy policy URL | Data safety form + privacy policy | Completed from our data inventory (§17.6) |
| Tracking | App Tracking Transparency (ATT) prompt if we track across apps | Advertising ID policy | **No cross-app tracking in MVP**, so no ATT prompt is needed. Revisit if ads are enabled |
| Trademarks | No unlicensed third-party marks | Same | Generated badges, no crests, "not affiliated" disclaimer (D-006) |
| User content | Report/block tools + moderation required for UGC | Same | §17.7 |

## 17.6 Privacy programme

**Data inventory (MVP):**

| Data | Purpose | Legal basis **[counsel]** |
|---|---|---|
| Email / sign-in ID | Account | Contract |
| Username, country, clubs | Game + leaderboards | Contract |
| "Over 16" confirmation | Age gate | Legal obligation |
| Predictions & scores | Core service | Contract |
| Device token | Push | Consent (OS permission) |
| Purchase status | Subscriptions | Contract |
| App analytics events | Product improvement | Consent / legitimate interest **[counsel]** |
| Crash logs | Stability | Legitimate interest |

**Not collected:** phone number, real name, precise location, contacts, birth date (only the 16+ flag), payment card data (the stores handle it).

**User rights:** access and export (JSON, within 30 days), correction, deletion (within 30 days; Part 12 §12.13), consent withdrawal (analytics toggle in Settings).

**Vendors (processors):** Supabase, Google/Firebase (FCM, Crashlytics), RevenueCat, the analytics provider, the football data provider (receives no personal data). Each needs a data processing agreement and a transfer assessment **[counsel]**.

**Security & breach:** Part 14. A breach-notification runbook with regulator timelines for KSA and UAE **[counsel]**.

## 17.7 Content, culture & moderation
- **Usernames and group names:** automatic filters (Arabic + English profanity and hate lists), a block on religious, political and national-symbol misuse and on impersonating clubs, players or officials, plus a report button. Offending names are reset to a default.
- **Profile photos:** optional. Uploads go through automated image moderation and can be reported. MVP option: initials avatars only, to reduce risk and cost.
- **No open chat or comments in MVP** (Part 5). This removes most moderation risk.
- **Ads (when enabled):** strict category blocklist: gambling, betting, lottery, alcohol, dating, adult content, political, religious-controversial, crypto trading.
- **Cultural calendar:** Ramadan-aware notification timing (e.g. no reminders during iftar, as an option), Eid seasonal content, national days (Saudi National Day 23 Sep, UAE National Day 2 Dec) as celebration themes only.
- **Sponsors:** every sponsor and message is checked against the Charter and the ad blocklist before approval.

## 17.8 Documents to prepare with counsel (before beta)

| Document | Languages | Notes |
|---|---|---|
| Terms of Service | AR (governing) + EN | Includes the No-Gambling Charter, no cash value of points/Coins, account suspension for cheating, governing law **[counsel]** |
| Privacy Policy | AR + EN | Based on §17.6 |
| Community Guidelines | AR + EN | Names, photos, cheating, harassment |
| Subscription Terms | AR + EN | Tiers, renewal, cancellation, refunds via stores, lapsed-subscriber item rules (Part 8 §8.8) |
| Data processing agreements + transfer assessments | EN | For each vendor |
| App review notes | EN | Explain "free-to-play prediction game, no prizes, no gambling" up front |
| Influencer & sponsor contract template | AR + EN | Mawthooq / Advertiser Permit warranty, Charter compliance, disclosure of paid content |
| Written legal opinion | – | Confirming that the product is outside gambling, commercial-gaming and prize-competition regulation in KSA and UAE **[counsel]** |

## 17.9 Open legal questions (added to open-questions.md)
- **Q-16:** hosting location under Saudi PDPL.
- **Q-17:** retention periods and anonymisation on deletion.
- **Q-19:** Where will the company be registered (KSA commercial registration, UAE mainland, or a UAE free zone)? This decides which data law applies, where contracts are signed, and how influencer campaigns are run.
- **Q-20:** A written opinion from counsel confirming the product sits outside gambling, GCGRA commercial gaming and KSA prize-competition rules.

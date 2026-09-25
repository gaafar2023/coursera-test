# Brief for Legal Counsel (KSA & UAE)

> Prepared 2026-09-25 for the founder's meeting with counsel. Please return written answers to Q-16, Q-17, Q-19 and Q-20.

## The product in one paragraph
A **free** football score-prediction mobile app (iOS/Android), Arabic-first, launching in Saudi Arabia and the UAE for users aged **16+**.
- Users predict match scores for 8 competitions (Saudi Pro League, UAE Pro League, Premier League, La Liga, Serie A, Bundesliga, Ligue 1, UEFA Champions League) and earn **virtual points** for accuracy. They compete on leaderboards (global, country, club fan bases, private friend groups).
- **Optional subscriptions** (two tiers) sell ad-free use, personal statistics, more groups and cosmetic items. They never affect points or rankings.
- Virtual "Coins" mirror the points earned and can be spent only on cosmetic avatar items. They are **never sold** and have **no cash value**.
- **There is no wagering, no paid entry, no prizes of any kind, and no random paid rewards** (see the No-Gambling Charter, Part 17 §17.1).

## Questions

**Q-20 — Gambling / gaming classification (most important)**
Please confirm in writing that, as designed, the product is **outside**:
(a) the gambling prohibition in KSA;
(b) KSA Ministry of Commerce commercial/prize-competition rules (we run no prize competitions);
(c) UAE commercial gaming regulated by the GCGRA (Federal Decree-Law No. 25/2025).
Please also flag any feature in Part 17 §17.1 or Part 8 §8.8 (Fan Store/Coins) that you consider risky.

**Q-16 — Hosting location (Saudi PDPL)**
We plan to host on a managed PostgreSQL service in a **UAE or Bahrain** cloud region.
Personal data held: email or sign-in ID, username, country, favourite clubs, an "over 16" flag, predictions, device push token and subscription status. We hold no phone numbers, real names, birth dates or payment data.
Question: is this acceptable for Saudi users with standard contractual clauses (SCCs) and PDPL transfer documentation, or must Saudi users' data be hosted **inside KSA** from launch? If in-KSA hosting is required, is a later migration acceptable (for example, a pilot or beta hosted outside KSA)?

**Q-17 — Retention and deletion**
Proposed rules (Part 12 §12.13):
- On account deletion, profile data is deleted within 30 days. Prediction records are kept but **re-keyed to a random identifier with no link to the person**, and removed from all leaderboards.
- Predictions and scoring history are kept for the current season + 2 seasons, then aggregated.
- Audit logs are kept 2 years. Purchase and store event records are kept 7 years.
- Analytics events are kept 14 months.

Question: do these periods, and the treatment of re-keyed records as anonymised, satisfy the KSA PDPL, the UAE PDPL (Federal Decree-Law 45/2021) and GDPR-grade standards?

**Q-19 — Company registration**
Where should the company be registered: a KSA commercial registration, UAE mainland, or a UAE free zone (DIFC/ADGM)? What are the consequences for data protection law, consumer law, signing vendor contracts, and running influencer campaigns (Mawthooq / UAE Advertiser Permit)?

## Documents we would like counsel to draft or review
Terms of Service (Arabic governing, with English), Privacy Policy, Community Guidelines, Subscription Terms, vendor data processing agreements, and an influencer/sponsor contract template. Details are in Part 17 §17.8.

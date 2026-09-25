# PART 14 — Security & Anti-Cheat Architecture

> Status: Draft v0.1 · 2026-09-25
> Threat priorities for this product: **(1) prediction tampering and late submission, (2) leaderboard manipulation
> (multi-accounts, bots), (3) account takeover, (4) data exposure, (5) admin abuse, (6) availability at lock time.**

---

## 14.1 Threat model (STRIDE summary)

| Threat | Example | Primary controls |
|---|---|---|
| **Spoofing** | Stolen session, fake Apple/Google token | Supabase Auth with verified OAuth tokens, short-lived JWTs (≤ 1 h) + rotating refresh tokens, MFA for admins |
| **Tampering** | Submitting after lock, editing old predictions, altering points | DB-clock lock inside RPC + triggers, append-only tables, RLS, no client-side scoring (Parts 11, 12) |
| **Repudiation** | "I submitted before kick-off!" | Revision log with server timestamps and idempotency keys, shown to the user as a receipt |
| **Information disclosure** | Reading others' predictions before lock, leaking emails | RLS (others' predictions visible only after lock), minimal data, emails never exposed in any API |
| **Denial of service** | Flooding submit near kick-off | Rate limits, pooled DB connections, provider-level DDoS protection, feature-flag degradation (Part 11 §11.7) |
| **Elevation of privilege** | A user calling admin RPCs | Role checks inside security-definer functions, separate admin role, audited admin actions |

## 14.2 Authentication & sessions
- **Methods:** Sign in with Apple, Google, and email (magic link or password with a strength check and breached-password check where supported **[verify Supabase feature]**).
- **MFA:** required (TOTP) for all admin/operator accounts. Optional for users in V1.1.
- **Tokens:**
  - Access JWT lifetime ≤ 1 h. Refresh tokens rotate and are revoked on password change or account deletion.
  - On the device, tokens live in the iOS Keychain / Android Keystore (`flutter_secure_storage`), never in plain preferences.
- **Session limits:** max 5 active devices per user. A new-device sign-in notification comes in V1.1.
- **Account recovery:** only through the original provider or verified email. Support staff can't change a user's email without identity checks.

## 14.3 API & database security
- **Row-Level Security on every table.** A CI test fails if any table lacks RLS policies.
- **Writes only through RPCs** with input validation:
  - scores are integers 0–15, IDs must exist, and strings are length-limited and normalised (Unicode NFKC)
  - usernames go through the profanity and impersonation filters (Part 17 §17.7)
- **Parameterised SQL only.** RPCs don't build dynamic SQL from user input.
- **Rate limiting** per user and per IP (Part 13 §13.8). Invite-code lookups are throttled to stop code guessing.
- **Security headers and CORS** for the admin web console. The app API only accepts the app's anon key + a user JWT.
- **The service-role key never leaves the server side** (Edge Functions, CI). The football provider API key is kept in server secrets only.

## 14.4 Data protection
- **Encryption in transit:** TLS 1.2+ everywhere.
- **Encryption at rest:** managed disk encryption (Supabase/AWS). Backups are encrypted.
- **Minimisation:** no phone, real name, birth date or card data (Part 17 §17.6).
- **Backups:** daily automated backups + point-in-time recovery (Pro plan) **[verify retention]**. A monthly restore test.
- **Secrets management:** Supabase secrets + GitHub Actions encrypted secrets. Keys are rotated every 90 days and immediately after any staff change. A secret-scanning step runs in CI.
- **Logging hygiene:** no tokens, emails or full IPs in application logs (IPs are truncated in analytics).

## 14.5 Anti-cheat & leaderboard integrity

### Layer 1 — Make cheating impossible (hard rules)
- Server-clock lock, append-only history, idempotent settlement, no client-side points (Parts 7, 11, 12).
- Others' predictions are hidden until lock, so there is no copying.
- Coins and points are non-transferable, so multi-accounts can't funnel value to a main account.

### Layer 2 — Make fake accounts expensive (signals at sign-up and submit)
| Signal | Use |
|---|---|
| **App Attest (iOS) / Play Integrity (Android)** tokens | Verified server-side at sign-up and on prediction submits. Failures are flagged (not blocked outright at MVP, to avoid false positives on older devices) |
| Verified email / OAuth identity | Required before appearing on public boards |
| Account age ≥ 7 days + ≥ 3 settled predictions | Required to count in club-vs-club rankings (Part 8) |
| Device fingerprint (install ID + attestation key ID) | Several accounts on one device → review |
| Disposable-email domain list | Blocks known throwaway domains |
| Velocity | Many sign-ups from one IP/ASN in a short time → captcha challenge or temporary block |

### Layer 3 — Detect patterns (batch job after each game week)
| Pattern | Heuristic | Action |
|---|---|---|
| **Hedging clusters** | ≥ 3 accounts sharing device/IP signals whose predictions cover different scorelines of the same matches | Hide from public boards, then review |
| **Impossible timing** | Submissions that follow a script-like cadence (e.g. identical intervals across 50 matches) | Flag |
| **Statistical outliers** | Exact-score rate far above the population (z-score > 4 over ≥ 50 predictions) | Manual review (could just be a genuine expert) |
| **Group farming** | Groups filled with new, inactive accounts | Group excluded from featured boards |

**Enforcement ladder:** flag → hidden from public boards (`leaderboard_eligible = false`, and the user still plays normally) → warning → suspension → ban. Every step is audit-logged with a reason, and users can appeal through support.
**Principle:** reward integrity quietly and never publicly accuse users. False positives are handled gently.

## 14.6 Payment security
- All payments go through Apple/Google. We never see card data, so our PCI scope is minimal.
- Entitlements come only from **server-verified** RevenueCat webhooks (shared-secret auth, idempotent event IDs) or the RevenueCat REST API, never from the client's claim.
- Refunds and chargebacks reported by the stores revoke entitlements automatically.

## 14.7 Admin security (RBAC)

| Role | Can | Cannot |
|---|---|---|
| **Support** | View users, predictions and settlement history; resend a data export; hide a username | Change results, points or subscriptions |
| **Operator** | Everything Support can do + override a fixture status/result, re-settle, manage fixtures and flags | Manage admins, delete audit data |
| **Admin** | Everything Operator can do + moderation actions (suspend/ban), manage achievements, plans display, store items | Manage superadmins |
| **Superadmin** (founder) | Manage admin roles | Edit or delete the audit log (no one can) |

- MFA is mandatory. Admin sessions time out after 30 min idle, and access is allow-listed by IP where practical.
- **Every admin write** is recorded in `audit_log`: before/after values, a mandatory reason, IP and time.
- **4-eyes rule (V1.1):** re-settling a match that changes points for more than 1,000 users requires a second admin's approval.
- Quarterly access review: remove unused admin accounts.

## 14.8 Mobile app hardening
- Release builds are obfuscated (Flutter `--obfuscate --split-debug-info`).
- **No secrets in the app bundle** (only the public anon key and public URLs).
- Jailbreak/root detection **only feeds the risk score**, and never blocks users outright.
- Deep links are validated (allow-listed hosts/paths only). Invite codes are verified server-side.
- WebViews are used only for our own legal pages, with JavaScript bridges disabled.

## 14.9 Availability & DDoS
- Managed platform protections (Supabase/AWS) + CDN caching for public data.
- Graceful degradation flags (Part 11 §11.7). `submit_prediction` gets priority.
- An **incident runbook** covers: provider data outage (pause settlement, show "results delayed"), DB overload (disable non-essentials), a bad settlement (freeze + re-settle), and security breach (Part 17 breach process).

## 14.10 Security testing (feeds Part 26)
- **Automated:**
  - dependency scanning (Dependabot)
  - secret scanning
  - static analysis (`dart analyze` with strict lints; ESLint + TypeScript strict)
  - an RLS policy test suite
  - SQL tests for lock and append-only guarantees
- **Before beta:**
  - an OWASP MASVS-based self-review of the mobile app
  - an OWASP API Top 10 review of the RPCs
- **Before public launch:** an external penetration test. It is the largest security cost item, so scope it to the API, RLS and admin console (Part 20).
- **Bug reporting:** a `security.txt` + a simple responsible-disclosure email.

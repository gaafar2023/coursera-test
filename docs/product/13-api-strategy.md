# PART 13 — API Strategy

> Status: Draft v0.1 · 2026-09-25
> Two halves: **(A)** the external football data provider, and **(B)** our own app-facing API.

---

# A. Football data provider

## 13.1 What we actually need (so we don't overpay)

| Need | MVP? | Notes |
|---|:-:|---|
| Fixtures with reliable kick-off times + changes | ● | For all 8 competitions (D-005) |
| Status transitions incl. postponed/cancelled/abandoned/awarded | ● | Drives Part 7 §7.4 |
| **Regulation-time (90 min) score separate from ET/penalties** | ● | Required by the knockout rule (Q-14) |
| Results within ~5 min of full time + corrections | ● | Part of the 15-min SLA (D-008) |
| Live score + minute | ◐ | For the live card (flagged) |
| Standings, last-5 form, head-to-head | ● | Free pre-match info |
| Home/away splits, goal trends | ◐ | Pro insights (could be computed from our own stored results) |
| Lineups, events, player stats | – | V2 |
| Logos / images with display rights | – | Not used in MVP (D-006) |
| Arabic team names | – | We maintain our own `name_ar` (about 150 clubs, one-time work) |

## 13.2 Provider comparison

| Criterion | **API-Football (API-Sports)** | **Sportmonks** | **football-data.org** | **Statorium** | **Enterprise (Stats Perform / Sportradar / Genius)** |
|---|---|---|---|---|---|
| League coverage | Very broad **[verify SPL + UAE PL in their coverage list]** | 2,200+ leagues claimed | 12 free; more on paid plans; SPL/UAE **[verify]** | Offers a dedicated Saudi Pro League API ([Statorium](https://statorium.com/saudi-arabian-saudi-pro-league-api)) | Full, official-grade |
| GCC coverage depth | **[verify in V5]** | **[verify in V5]** | Weak on the free tier | Explicitly markets the SPL | Strong |
| Fixtures / results / live | Yes / yes / yes | Yes / yes / yes | Yes / yes / limited | Yes | Yes, lowest latency |
| Status codes | Rich: NS, 1H, HT, 2H, ET, BT, P, FT, AET, PEN, PST, CANC, SUSP, ABD, AWD, WO… ([API-Football guide](https://www.api-football.com/news/post/how-to-get-started-with-api-football-the-complete-beginners-guide)) | Rich state model ([Sportmonks states](https://docs.sportmonks.com/v3/definitions/states)) | Basic | **[verify]** | Rich |
| Separate 90-min / ET / pens scores | Score breakdown by halftime, fulltime, extratime, penalty (per the guide above; confirm that "fulltime" = 90 min) | Score types per period **[verify]** | **[verify]** | **[verify]** | Yes |
| Standings / team stats / H2H | Yes | Yes | Standings | Yes | Yes |
| Pricing (as found, verify) | Free 100 req/day; ~$19 / $29 / $39 per month for 7.5k / 75k / 150k req/day; all endpoints on all plans (third-party reports) | Starter €29 (5 leagues), **Growth €99/€79 yearly (30 leagues)**, Pro €249 (120) | Free; paid up to ~€199 | Quote | Custom, typically far above our budget |
| Pricing model fit | **Per request volume, all leagues**, which suits 8 competitions cheaply | Per league count, so 8 competitions needs Growth | – | – | – |
| Webhooks / push | Polling (no push) **[verify]** | Polling | Polling | **[verify]** | Push feeds |
| Commercial licence | Paid plans for commercial use **[read ToS]** | Commercial on paid plans **[read ToS]** | Free tier is non-commercial **[verify]** | **[verify]** | Contracted |
| Docs / developer experience | Good, large community | Good | Simple | Unknown | Good |

Sources for pricing are in Part 2 §2.6. Everything marked **[verify]** is checked during experiment V5.

## 13.3 Recommendation

**Primary for MVP (D-030): API-Football on the ~$19/month tier, subject to passing the V5 trial.**
It is the cheapest option that (a) prices by request volume rather than league count, (b) exposes the full status vocabulary our settlement rules need, and (c) has a free tier for development.

**Fallback / second source:** Sportmonks Growth (~€79–99/month). We only pay for it if API-Football fails V5 on SPL/UAE timeliness or accuracy.

**Low-cost cross-check:** for the 8 competitions, a daily reconciliation job compares our settled results with a second free source (e.g. football-data.org's free tier for the 5 European leagues + UCL). Any mismatch goes to the admin console.

**V5 trial acceptance criteria (2–3 matchweeks, free/trial tiers):**
1. 100% of SPL, UAE PL and UCL fixtures present, with correct kick-off times ≥ 24 h ahead.
2. The FT status appears ≤ 5 min after the real final whistle for ≥ 95% of matches.
3. The regulation-time score is exposed separately for AET/PEN matches.
4. Zero wrong final scores after 24 h.
5. The ToS permits storing results and displaying them in a commercial app.

## 13.4 Request budget (API-Football, 8 competitions)

| Job | Frequency | Calls/day (peak day) |
|---|---|---:|
| Fixture sync per competition (next 14 days) | every 6 h × 8 | 32 |
| Standings per competition | 2×/day × 8 | 16 |
| Live fixtures (a single "all live" call, filtered to our competitions) | every 60 s **only while any tracked match is live** (~10 h on a busy Saturday) | ~600 |
| Post-match result confirmation per finished fixture | FT + 2 min, + 30 min, + 6 h (correction watch) | ~3 × 40 = 120 |
| Team form / H2H for upcoming fixtures (cached 24 h) | 1× per fixture | ~40 |
| Daily reconciliation | 1× per competition | 8 |
| **Total (peak day)** | | **~820** |

That is comfortably inside the 7,500/day tier. The free tier (100/day) is enough for development only.
Caching rule: the provider API is **never called from the app**. All provider data flows through our ingestion and database.

## 13.5 Ingestion design (Part 11, Edge Functions)

```
fetch → store raw payload (provider_payloads, dedupe by hash)
      → map provider status → our status (table below)
      → diff with our fixtures row → emit domain changes:
          KickoffChanged → recompute lock_at (+ notify)
          StatusChanged(postponed/cancelled/…) → Part 7 rules
          Finished / ResultChanged → new fixture_results version → settle-match
      → anomalies (score decreasing, FT → live, unknown code) → status 'hold' + admin alert
```

| API-Football code | Our status | Settlement action |
|---|---|---|
| TBD, NS | scheduled | – |
| 1H, HT, 2H, ET, BT, P, LIVE | live | – (live card uses the 90-min score only) |
| SUSP, INT | suspended | wait |
| FT, AET, PEN | finished | settle on the **90-min** score |
| PST | postponed | Part 7: reopen or void after 21 days |
| CANC | cancelled | void |
| ABD | abandoned | hold → admin decides: resumed / replayed / result stands / void |
| AWD, WO | awarded | void |
| Unknown | hold | admin alert |

**Provider independence:** all provider-specific code sits behind one `FootballDataProvider` interface (`getFixtures`, `getLive`, `getFixture`, `getStandings`, `getHeadToHead`) with a mapping layer. Adding or switching a provider means writing a new adapter plus recorded-payload tests.

# B. Our app-facing API

## 13.6 Style & conventions
- **Transport:** HTTPS only, through Supabase PostgREST (reads) and Postgres **RPC functions** (all writes with business rules). The app never writes directly to tables that have rules.
- **Auth:** Supabase JWT (short-lived access token + refresh token). RLS is applied to every request.
- **Versioning:** RPC names carry a version (`submit_prediction_v1`). Breaking changes add `_v2`, and old versions are kept until the minimum supported app version moves past them. The app sends `X-App-Version`, and the server can answer `426 Upgrade Required` via a remote-config gate.
- **Idempotency:** every write RPC takes a client-generated `idempotency_key` (UUID v4). Replays return the original result.
- **Server time:** every response includes the server time (a header, or a `server_time` field in the RPC result).
- **Errors:** a stable, machine-readable `code` plus a localised message key, e.g. `PREDICTION_LOCKED`, `FIXTURE_NOT_OPEN`, `LIMIT_GROUPS_JOIN`, `RATE_LIMITED`, `ATTESTATION_FAILED`.
- **Pagination:** keyset (cursor) pagination for leaderboards and history, never offset.
- **Localisation:** the server returns keys and names in both languages. The client picks the language.

## 13.7 Endpoint catalogue (MVP)

| Area | Operation | Type | Notes |
|---|---|---|---|
| Config | `get_bootstrap_v1()` | RPC | Server time, feature flags, min app version, entitlements, user preferences, in one call on app start |
| Catalogue | `competitions`, `clubs` (read) | REST | Cacheable (ETag, max-age 1 h) |
| Fixtures | `list_fixtures_v1(from, to, competition_ids[])` | RPC | Includes the user's prediction state per fixture |
| Fixture | `get_fixture_v1(id)` | RPC | Match info + form + H2H + my prediction + community distribution (only if I've predicted or it's locked) |
| Predict | `submit_prediction_v1(fixture_id, home, away, idempotency_key, attestation_token)` | RPC | Part 11 §11.5 |
| Predict (bulk) | `submit_predictions_v1(items[], idempotency_key)` | RPC | Each item is validated independently; partial success is returned per item |
| History | `my_predictions_v1(cursor, filters)` | RPC | 30-day limit for Free (entitlement) |
| Leaderboard | `get_leaderboard_v1(scope, scope_id, period, period_key, cursor)` | RPC | Plus `around_me` mode |
| Club vs club | `get_club_rankings_v1(period, period_key)` | RPC | |
| Groups | `create_group_v1`, `join_group_v1(code)`, `leave_group_v1`, `remove_member_v1`, `regenerate_invite_v1`, `get_group_v1` | RPC | Limits come from entitlements |
| Profile | `get_profile_v1(user_id)`, `update_profile_v1(...)`, `set_primary_club_v1(club_id)` | RPC | Primary club: once per season |
| Preferences | `update_preferences_v1(...)` | RPC | Leagues, clubs, notifications |
| Devices | `register_device_v1(fcm_token, platform)` | RPC | |
| Account | `request_data_export_v1()`, `delete_account_v1(confirmation)` | RPC | Asynchronous jobs |
| Webhooks | `/functions/v1/revenuecat-webhook` | Edge Function | Shared-secret auth + idempotent by event ID |
| Admin | `admin_override_result_v1`, `admin_resettle_v1`, `admin_moderate_user_v1`, `admin_set_flag_v1`, … | RPC | Admin role + MFA session. Every call is written to `audit_log` with a mandatory reason |

## 13.8 Rate limits (initial values)

| Operation | Limit |
|---|---|
| submit_prediction(s) | 60/min per user, 20/min per fixture per user |
| join_group (code attempts) | 10/hour per user (stops invite-code guessing) |
| Sign-up | 5/hour per IP + attestation required |
| Leaderboard reads | 120/min per user |
| Global | Supabase/API gateway limits + the provider's DDoS protection (Part 14) |

Limits are enforced by a Postgres-side token-bucket table for RPCs (cheap at MVP scale), and move to the gateway or Redis at scale.

## 13.9 Decisions (added to the log)
- **D-030:** API-Football is the primary provider (pending V5), with Sportmonks as the fallback.
- **D-031:** The provider is never called from the app. One adapter interface, and raw payloads are kept 90 days.
- **D-032:** All writes go through versioned, idempotent RPCs that return the server time.

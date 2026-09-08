# Changelog

All notable changes to the Nakordoni Developer API.

See also the live changelog: https://nakordoni.eu/en/developers/changelog

---

## 2026-09-07

### Breaking
- **Announced retirements are closed to accounts created after the announcement** — **From today, anything we have publicly announced as retiring is closed to developer accounts created on or after the announcement date.** If your account existed before the announcement, nothing changes — you keep the full grace period, right up to the retirement date given in the entry that announced it. **Why this rule exists.** On 24 August 2026 we announced that `truck-bans` v1 retires on 8 September 2026. Two accounts registered days after that announcement, built their integration on v1, and came within hours of a `410` no email of ours had ever reached them: the announcement and the notification batch both predated their signup. Nothing in the API stopped them adopting a version we had already said was going away. That was our failure, and this is the fix — you cannot newly adopt something already scheduled to be removed. **What it looks like.** Such a call is refused with **410 Gone** and error code `version_closed_to_new_accounts`. The message names the retirement date, the announcement date and the version to use instead. It is deliberately a different code from `version_sunset`, which is what every account gets once the retirement date itself has passed — support can tell "you arrived too late to start" from "this is gone for everyone" without reading a log. **Grandfathering is by account creation date, not by first call.** If you registered before the announcement but only start integrating now, you still get the full grace period: you may have been building against it all along. **In effect now** for `truck-bans` v1 (announced 24 August 2026, retires 8 September 2026), and automatically for every retirement we announce from here on. Nothing new is required of you: every response on a retiring version already carries `Deprecation`, `Sunset` and `Link: rel="successor-version"` headers, so a new integration can see a retirement coming without reading this page.

### Added
- **Extra countries and Extra forecast calls add-ons; countries included per plan from 10 November 2026** — Two add-ons are available on the Billing page (monthly tab) on top of any plan, without changing it: **Extra forecast calls** — +100 forecast and statistics calls a day per block, €2 a month per block, up to 10 blocks; and **Extra countries** — +1 declarable country per unit, €2 a month each. Changing a quantity shows an exact prorated quote before anything is charged. From **10 November 2026** each plan includes a set number of declared countries: Explorer and Student 4, Starter 10, Pro and above unlimited. From that date a declaration longer than plan + purchased extra countries cannot be saved; the Account tab shows your allowance now, and accounts already above it see a suggestion on the dashboard. Nothing changes before 10 November.

---

## 2026-09-06

### Breaking
- **Border Queue v4: one destination country per call, and destination=all retires on 2026-10-06** — **/api/v4/data/border/{origin}/{destination}/{crossing_type}** is live today. Three things change from v2, and together they are why it is a new version rather than an edit. **1. No more `destination=all`.** Our data is licensed per country (Developer API Terms, section 7), and a wildcard that expands to "every neighbour we hold data for" returns countries your account may not be approved for — with nothing in the request to show it. In v4 you name the country. **2. One destination country per call.** `/api/v4/data/border/1/2/9` asks for one border. Comma lists are not accepted: send `1/2/9`, `1/3/9` and `1/4/9` as separate calls. A comma list or `all` answers **400** and names the exact calls to send, so nothing fails silently. **3. One truck code.** v1 and v2 split freight into `8` (Freight Transport) and `9` (Freight Transport up to 7.5 t). That split is real at the crossing, but no integrator can act on it: asking v2 for `9` on the UA-PL border returned 21 of the 70 truck crossings and nothing said so. **v4 answers `9` with every truck lane**, and accepts `8` as an alias of `9`. Each row carries its own `crossing_type`, so a merged answer stays inspectable. **In v1 and v2, `destination=all` keeps working until 2026-10-06** and carries `Deprecation` / `Sunset` headers until then. From that date those versions answer 400 for `all` as well — the rest of v1 and v2 is untouched and stays available. The same date applies to the other all-countries shortcuts: `travel-matrix` without `?dest=`, `bus-carriers` with `?ppid=all`, and `fuel-grades` without `?country=`. **v3, announced earlier today, is superseded by v4.** v3 differed from v4 only in still accepting a comma list, and no integration uses that shape. v3 URLs keep answering so nothing written against them breaks, but v3 is not documented and will not be developed further — **migrate to v4**. Everything else in v4 is v2: directional path order, `direction{from,to}`, `stale`, and `?max_age_min=`.

### Fixed
- **Country ids and vehicle-type codes are now documented — and 8/9 were the wrong way round** — The numeric ids in `/border/{origin}/{destination}/{crossing_type}` were never published as a table, so integrators reconstructed them from timezones and sample URLs. They are now in the docs under [Country and vehicle-type codes](/en/developers/docs#codes), rendered from the same tables the API validates against — country ids with the borders each one expands to, and every `crossing_type` with the label the API returns. While publishing them we found the sandbox and the endpoint metadata describing `8` as "truck<7.5t" and `9` as "truck". That is inverted: the API labels `8` Freight Transport and `9` Freight Transport up to 7.5 tons, and always has. If you picked a truck code from the parameter hint, you were filtering the opposite lane to the one you meant. Corrected everywhere, and v3 removes the choice entirely.

### Improved
- **Developer API Terms v1.1 — what a "market" means, and two changes in your favour** — [API Terms v1.1](/en/p/developer_api_terms) replace v1.0 before it took effect, and apply from **2026-10-06**. Please accept them in your dashboard. **Section 7 now says what a Market is:** the country whose data you use — where the checkpoint or border you request is — not the country your users live in. Our dashboard had said both things in different places; the enforcement always meant the first. **Two changes in your favour.** Countries already approved for your account stay usable while a later change is under review (adding a country no longer suspends the ones you have). And if we have not answered a market declaration within 5 business days, your full plan limits apply until we do. Section 10.3 now matches what the dashboard actually asks for, and section 13.2 states an availability basis we measure and can show you.

---

## 2026-09-05

### Improved
- **New: data_quality flag on Queue, Live Queue & Freshness, and Multi-Checkpoint** — Three products now carry an additive `data_quality` field (`high` or `low`) marking whether a reading is a real observation or a model estimate with no live counting source at that crossing: `queue` (on the top-level `snapshot` and on each historical row in `data[]` — absent on forecast rows), `update-info` (on the envelope), and `multi` (on both the `queue` and `update_info` sub-objects per checkpoint). This is not a new signal — the underlying flag already existed internally — but it was never exposed, so a fully modelled checkpoint looked identical to a directly measured one. `is_realtime` is intentionally unchanged: it still reads `true` for modelled rows, and changing that meaning is a v2-level breaking change we are not making here. Also from this release: the `queue-advanced` product no longer redistributes raw upstream weather. `weather_main`, `temperature` and `wind_speed` are replaced by a derived `condition_code` (0–5 hazard scale, `null` when no weather is available), `condition` and `severity`.

---

## 2026-08-25

### Deprecated
- **Multi-Checkpoint API: 5 checkpoints per request from 2026-08-30** — From **2026-08-30** one `/api/v1/data/multi` request is answered for at most **5** checkpoints. A call that lists more PPIDs is *not* rejected: it still returns 200, but only the first 5 IDs in `?ppids=` are answered. The remaining IDs are ignored, echoed back in `meta.ppid_cap.ignored`, and are not charged against your quota — the call is billed on what it actually returns. While a call is over the limit the response carries an `X-Devapi-Warning: multi_ppid_cap` header and a `meta.ppid_cap` block with `cap`, `enforced_from`, `enforced`, `ppids_asked`, `ppids_answered` and `ignored[]`. Until 2026-08-30 those fields appear with `enforced: false` and the full result set, so you can see the change coming in your own logs. The quota discount of one half is unchanged. Split your checkpoints into groups of 5 and send one call per group on your normal refresh cycle; for frequent polling of queue length and freshness only, `update-info` stays the cheaper standard-class product.

### Improved
- **Ukraine heat ban now follows your date window (v2)** — Ukraine’s computed heat ban — returned with `include_ua_heat`, and automatically for `country=UA` — now answers for the date window you ask for. It previously returned the coming seven days whatever `date_from` and `date_to` said, so a December window quietly came back with this week’s rows. The ban is computed from a weather forecast rather than read from the ban calendar, so it has two edges the calendar does not: it cannot look backwards, and it stops where the forecast stops. Your window is now intersected with what the forecast actually covers, and a new `ua_heat_ban.forecast_horizon` field names the last date it reaches. A window beyond that horizon returns no heat rows and says why in `summary` — which is not the same as “no ban”. v1 responses are unchanged.
- **Every product now documents its response fields** — Response shapes were previously undocumented — the only way to learn what a product returned was to call it. Every product page now shows a **Response fields** table underneath its parameters table, listing each field with a short description; list-element fields are shown as `items[].name`, envelope-level fields (usage, meta, snapshot, resolved_location) are shown bare. 40 of 42 products are documented — the two products not yet launched (`weather`, `road-quality`) intentionally have none yet. The same table is published to our public GitHub docs mirror.
- **Truck Bans API v2: ask for a date or a date range** — The `truck-bans` product now answers for a specific date or date range on `/api/v2/data/truck-bans`. Until now it always returned the coming 7 days and ignored any date you sent, so building a calendar meant one request per day — and on a two-requests-per-second plan most of those are rejected with `429 qps_exceeded`. Use `?date=YYYY-MM-DD` for one day, or `?date_from=` and `?date_to=` for a range. Both ends are inclusive and either may be omitted: the start defaults to today, the end to the start plus 7 days. A window may cover at most 92 days — a longer one is refused with `400 date_range_too_long` instead of being quietly cut. This is a forward-looking calendar: a window may start at most 7 days in the past, and anything older is refused rather than served — coverage runs forward to 31 December 2028 across 23 countries. Every response now carries a `window` object naming the exact range it covers. It is additive and is sent on v1 as well, and v1 keeps its fixed 7-day window unchanged. Note that `include_ua_heat` always covers the coming 7 days whatever window you ask for — it comes from a weather forecast, not from the ban calendar. Remember that v1 of this product retires on 8 September 2026. Two related improvements across the whole API: any parameter a product does not accept is now listed in `ignored_params` on the response instead of being dropped in silence, and validation errors from the data service now reach you as written, with the machine-readable token in `error.reason`.
- **X-API-Key header accepted, clearer missing-key error, has_day_stats on the checkpoints directory** — Three response-quality fixes from a gateway audit (ticket #43). **The `X-API-Key` header is now accepted** alongside `Authorization: Bearer` and `?key=`. If your HTTP client sends keys via a header named `X-API-Key`, that now works — previously it was silently ignored and the call was rejected as `missing_api_key`. `Authorization: Bearer` remains the documented, recommended form. **The missing-key error message now names all three ways to authenticate** (Bearer header, X-API-Key header, or `?key=`) instead of only linking the signup page. **The `checkpoints` directory now carries `has_day_stats`** on every row — an additive boolean telling you whether the Best Time to Cross (day-stats) API has data for that checkpoint. Day-stats only exists for a subset of monitored checkpoints; check this flag before polling to avoid predictable 404s. Existing fields are unchanged. Also corrected in the docs: the `road-conditions` product always honoured a `lang` parameter for label localisation — it just was not listed.

### Added
- **New: Fuel Grade Names API + local pump names accepted everywhere** — Every fuel product now accepts the local name of a fuel grade, not just our internal spelling: `ON` in Poland, `Nafta` in Czechia, `Gázolaj` in Hungary, `Motorină` in Romania, `ДП` in Ukraine, `Motorin` in Turkey, `Gasóleo` in Portugal and Spain. Names are resolved country-first, because the same wording is not the same grade everywhere — “95” is E10 at a Danish pump and E5 at a Polish one — so send `country` together with a local name, or coordinates the point can be placed by. Responses echo `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade: the answer comes back empty and says so. The full table is now a product of its own — `GET /api/v2/data/fuel-grades[?country=PL][&fuel_type=ON]` — listing our canonical grades and their local names in 41 European countries, including markets we quote no price for. The country and region tiers of `fuel` and `fuel-local` also gained a `grades` object mapping each price key to its grade and local pump name.

---

## 2026-08-24

### Improved
- **Truck Bans API: comma-separated countries, completeness fields, and a scoped v2** — Two fixes and one new version for the `truck-bans` product. **Comma-separated countries now work.** `?country=` accepts a list of up to 3 ISO-2 codes, for example `?country=DE,RO`. A longer list is refused with `400 too_many_countries` rather than silently trimmed — this is a per-country ban calendar, not a bulk feed. This previously did not work: the separator was stripped, so `DE,RO` was read as the single token `DERO`, matched nothing, and returned `success: true` with `total_bans: 0` — a confident “no bans” for two countries that between them had 22. If you worked around it by issuing one request per country, a single request now covers all of them and costs one call instead of several. **Responses now report their own completeness.** Three additive fields — `returned`, `total_available` and `truncated` — tell you whether an answer was capped. An unscoped call in particular returns a capped slice, and until now nothing in the payload said so. `total_bans` keeps its existing meaning (rows in this response), so nothing you already parse changes. **v2 is scoped per country.** On `/api/v2/data/truck-bans`, `?country=` is required and an unscoped request is refused with `400 scope_required` — this product is a per-country ban calendar, not a bulk feed. **v1 is unchanged today** — it still accepts an unscoped call and still returns the same capped 50 rows it always did, so nothing you have running breaks right now. **v1 of this product retires on 8 September 2026.** It serves normally through 7 September; from 8 September a v1 request is refused with `410 Gone` and a message pointing at v2. Until then every v1 response carries `Deprecation: true`, a `Sunset` header with that date, and a `Link` header naming the successor version, so a client library can surface the deadline without anyone reading this page. To migrate: change the version segment to `/api/v2/data/truck-bans` and pass `?country=`. One documentation correction: the `date` parameter has been removed. It was listed for a long time but was never read by the service, so any request sending it silently received the default 7-day window rather than the day it asked for. To select a day, filter the `upcoming_bans` array by its `date` field. An ISO-3 code such as `DEU` also no longer resolves to a country name in the summary, where it produced the misleading “No truck ban data for: Germany.”
- **Truck Bans API: five new countries, and coverage extended into 2027** — The `truck-bans` product now returns country-level driving restrictions for five more countries: Belgium (`BE`), Belarus (`BY`), Montenegro (`ME`), North Macedonia (`MK`) and Sweden (`SE`). Existing coverage for Bulgaria, Greece and Portugal has been extended and refreshed — Greek restrictions now run to September 2027, and Portugal is populated again. The response shape is unchanged. New rows carry the same keys as every other ban: `date`, `time_from`, `time_until`, `restriction_type`, `restriction_details`, `min_weight_tons` and `details_url`. Where a restriction applies only under a condition — Belarusian summer bans apply above 25 °C, for example — that condition is stated in `restriction_details`, so read this field before warning a driver. `min_weight_tons` is `null` when a rule targets a transport class (dangerous goods) rather than a tonnage.
- **Fuel stations: station-level prices in Poland, and a sparse_coverage flag** — The `fuel-stations` and `fuel-cheapest` products now return station-level prices in Poland. Coverage is partial — the Tricity area (Gdańsk, Gdynia, Sopot) — so Poland is reported in a new additive `coverage.sparse_coverage` array alongside the existing `coverage.station_countries` list. A country listed in `sparse_coverage` has station data for part of its territory only; a query elsewhere in that country returns an empty list together with the coverage note, exactly as before. Polish prices are quoted in `PLN`. The bulk-query error is clearer too: when `lat` is missing, the `scope_required` message now points at the `fuel` product (`?country=XX`) for country-wide average prices.

---

## 2026-08-22

### Improved
- **Local Fuel Price API: new region tier for Ukraine** — `GET /api/v2/data/fuel-local?lat=&lon=` now resolves down three tiers instead of two: `station`, then `region`, then `country`. The new middle tier exists for Ukraine, where per-station prices exist nowhere: a Ukrainian point now answers with the average for its oblast instead of the national average, and falls back to the national average only when the oblast is not quoted. A response from the `region` tier carries the oblast code (an ISO 3166-2 value such as `UA-46`), `region_name` and `region_center_dist_km`, plus the same price keys as the country tier. Keep branching on `resolution`, never on the shape of the response; `station` and `country` answers are unchanged.

### Added
- **New product: Local Fuel Price API** — New endpoint `GET /api/v2/data/fuel-local?lat=&lon=` returns the best available fuel price for any point in Europe. It answers with pump prices from the nearest stations where station-level data exists, and falls back to the national average of the country the point falls in — including Ukraine, where per-station prices exist nowhere. Every response carries a `resolution` field naming the tier that answered: `station` (a list of stations with `distance_km`, each in its own currency) or `country` (one national-average object). Branch on `resolution`, never on the shape. Available from `/api/v2/` onwards; `fuel`, `fuel-stations` and `fuel-cheapest` are unchanged.

---

## 2026-08-19

### Improved
- **Fuel stations: 13 fuel types and fresher German coverage** — The `fuel-stations` and `fuel-cheapest` products now cover far more stations in Germany, with prices refreshed throughout the day — rural areas included. The `fuel_type` parameter accepts 13 fuel types: `diesel`, `e5`, `e10`, `superplus`, `super100`, `premdiesel`, `truckdiesel`, `hvo`, `lpg`, `cng`, `adblue`, `e85` and `lng`. When no station matches a query, the response includes a `coverage` object listing the countries with station data.
- **Data quality fixes: radius= alias, fuel coverage notes, truck border-plan accuracy** — The `radius=` parameter is now accepted as a compatible alias for `radius_km` on every product that documents it. The `fuel-stations` and `fuel-cheapest` products return an additive `coverage` object (station-country list plus a note) instead of a silent empty result when no stations match. `route-plan` border objects now include an additive `wait_basis` key (`car_lane` vs `vehicle_lane`) so clients can tell when truck wait data is a car-lane stand-in. Truck border matching along a route is substantially more accurate: car-lane fallback for border pairs with no truck-lane data, a wrong-direction guard, a tighter distance gate, and de-duplication of same-position crossings. All changes are additive; no breaking changes.

---

## 2026-08-13

### Improved
- **Portal landing redesigned: section anchors, mobile apps, full i18n** — The developer landing page now has anchored sections (#products, #plans, #quickstart, #integrations, #datasets, #apps, #companies, #showcase) with a jump navigation, and every product card links to its own docs page. New **Mobile apps** section presents Kordon Online and Truck Bans with Google Play links. Translation backfill: billing history, login errors, sandbox links and the plan-selection button are now localized in all 25 languages.

---

## 2026-08-12

### Added
- **NakBus Live: two-way driver messaging** — Fleet beacon response (`POST /api/v1/fleet_position.php`) now includes a `messages` array delivering pending owner→driver messages. New owner-only live JSON feed (`?ajax=live`) and a "Messages to drivers" card on the fleet dashboard. New driver invite landing page `/{lang}/get-nakbus` (25 languages).
- **Fleet API docs translated to 25 languages** — Localized product_fleet_vehicles/live/history title, desc and fleet-history params across all 25 dev-portal languages.
- **9 new APIs for drivers: truck parkings, shops, showers, restaurants, industrial zones, fuel stations, cheapest fuel, internet points, vignettes** — Nine new per-service products. The location ones accept `lat`/`lon` or `city` + `country` (we geocode the city for you): `/api/v2/data/truck-parkings`, `/api/v2/data/shops`, `/api/v2/data/showers`, `/api/v2/data/restaurants`, `/api/v2/data/industrial`, `/api/v2/data/fuel-stations`, `/api/v2/data/fuel-cheapest` (stations ranked by price for a fuel type) and `/api/v2/data/internet-points`; results carry `distance_km` and are bounded by `radius`. `/api/v2/data/vignettes` answers whether a country requires a vignette, with current prices. The existing `pois` product now honours `lon` and `radius` as documented, and the `fuel` product's `mode=nearest` accepts `lon` too. All nine are available in the sandbox.

### Improved
- **Truck Bans API: unified response keys across all branches** — `/api/v1/data/truck-bans` now returns the same set of top-level fields regardless of which query triggered the response. Previously a query for a country with no calendar bans, an unrecognized `ppid`, or a normal database match could each omit different fields (e.g. `country`, `covered_countries`, `ppid`). Every response now consistently includes `as_of`, `bans_by_country`, `countries_not_covered`, `country`, `covered_countries`, `current_bans`, `is_ban_active`, `lang`, `page_url`, `ppid`, `relevant_countries`, `source`, `success`, `summary`, `total_bans` and `upcoming_bans` (null or empty where not applicable), simplifying client-side parsing.
- **Truck Bans API: restriction details, own-domain links, lang parameter** — Each ban in `/api/v1/data/truck-bans` now includes `restriction_type` (General / Local / Sunday / Holiday / Seasonal), `restriction_details` (exact scope or roads affected) and `min_weight_tons`. `details_url` now points to per-country pages on nakordoni.eu. A new optional `lang` parameter selects the language of country names and the summary; the default is now English.

---

## 2026-08-09

### Improved
- **Clearer ppid errors and docs** — A malformed `?ppid=` now returns the real reason instead of a bare "Request failed": the error names the parameter, the expected `id_<number>` format and points to `/api/v1/data/checkpoints`. The parameter tables for `stats`, `forecast`, `update-info`, `weather` and `bus-carriers` now show the `id_13` example in all 25 languages.

---

## 2026-08-08

### Added
- **Portal V2 design is now the default** — The redesigned portal shell (top bar, icon sidebar, KPI dashboard, card-based layouts) is now the default experience for all logged-in developer accounts, brought forward from the planned 10 August rollout date. Use `?v=1` to switch back to the classic layout at any time.

### Improved
- **Developer portal is now ad-free** — All developer portal pages — landing, docs, dashboard, AI Studio, sandbox, tickets, requests, export, fleet, news, changelog, and account pages — no longer load any ad scripts or ad slots. This applies site-wide across the portal, not just login/signup as before.

---

## 2026-08-05

### Added
- **New product: Route Planner API (v2)** — Plan a whole border trip in one call: `/api/v2/data/route-plan` returns the route, the crossings actually on it with a live queue or a forecast for your arrival time, and the stops a driver really makes — rest breaks, a meal, refuelling — on a single timeline. The border is part of that timeline. A long queue counts as the break that was already due and resets the driving clock, so a three-hour wait is never reported as three hours *plus* a full set of breaks nobody took. Cars follow a driving-hygiene model; buses and trucks get the mandatory EU 561/2006 rest, and bus service overhead is calibrated on more than 1000 licensed international coach schedules. Add `stop_places=1` to name a real rest area or fuel station for every stop, and `via=lat,lon` to route through a different crossing.
- **Partner presentation — live data, personalized for your market** — New in the portal menu: **Presentation** — a live, always-current pitch of the nakordoni data platform personalized for your market (insurance, travel, logistics, carriers, media, navigation, fuel, fintech, public sector or personal projects). It shows real 30-day platform volumes, your own API usage, response-time and limit statistics, and a plan recommendation when your calls hit the free-tier limits. Pick or confirm your market(s) on the page, in your profile — or during signup. It opens automatically on your first visit; you can turn the auto-open off on the page itself.

---

## 2026-08-01

### Fixed
- **AI Studio: feeds without their context are skipped, not billed** — If an assistant has a feed enabled but the call does not carry the context that feed needs — `queue` without a `ppid`, for example — the feed is now skipped before any request is made and **is not charged**. Previously it was called anyway, failed, and still cost a unit. The studio shows what each feed needs, recalculates the price as you fill the context in, and marks results ✓ ran / ⊘ skipped, not charged / ✕ failed; the API returns `data.feeds_skipped` telling you exactly which parameter to pass. Answers no longer mention feeds, data sources or anything technical: a missing feed is at most one plain sentence to the end user, never an internal name. Feeds with only optional filters (such as `fuel` narrowed to a country we have no data for) now fall back to the broad dataset instead of returning nothing.

### Added
- **AI Studio: build your own assistant on your content + our live data** — **New: /{lang}/developers/studio.** Build an AI assistant that answers from *your* content and *our* live border data. Give us your markdown, or just name the pages and we fetch and index them — you only ever maintain your own files. Pick which of our feeds it may use (queue, forecast, alternatives, day-stats, fuel, truck bans, trading Sundays, holidays, road conditions, bus carriers, POIs, currency), pick a model tier (fast / balanced / pro — that is what sets the price), write your own instructions with `{{feed.slug}}` placeholders saying exactly where our data lands in the answer, and add a closing sentence of your own that is appended to every reply. Ready-made blueprints: personal travel assistant, work/freight assistant, insurance & Green Card sales assistant. Test it in the studio (30 answers/day, separate from your API quota), then call it in production at `GET /api/v2/data/assistant-custom?assistant_id=N&q=…`. Price per answer = model tier units + 1 unit per enabled feed, returned in `X-Devapi-Units`. The product is v2-only — a v1 URL returns `unsupported_version`. The existing `assistant` product is unchanged. Every assistant runs under a platform content policy that outranks your instructions: no impersonating officials, no help evading border or customs control, no invented numbers, no profanity. Instructions and answers are both screened; blocked calls are logged.

---

## 2026-07-26

### Added
- **MCP Server (Streamable HTTP)** — New: a real MCP server at `https://nakordoni.eu/mcp`, exposing a safe read-only subset of the API (status, checkpoints, border queue, live queue, forecast) as MCP tools. Same API key and quota as the REST API. Server card at `/.well-known/mcp/server-card.json`. See the [MCP Server](/en/developers/docs#mcp) section in the docs.

---

## 2026-07-21

### Fixed
- **Docs page still said "Data Freshness API" after the rename — now fixed in all 25 languages** — The retitle to "Live Queue & Freshness API" below did not actually reach the [docs](/en/developers/docs) page. The page renders each product title through a translation lookup that falls back to the endpoint's title only when no translation exists — and a translation already existed, frozen at the old name, in all 25 UI languages. It now wins over any future update to the underlying title until it is updated too. Retitled the translation key in all 25 languages so the docs page matches. No endpoint, parameter or response change — title text only.

### Changed
- **Data Freshness API is also your standard-quota live queue endpoint** — If you poll live queue data frequently, you may be spending heavy quota you do not need to. `/update-info` is **standard-class** and already returns the live figure: `GET /api/v1/data/update-info?ppid=id_13` It returns `queue_now`, `freshness`, `age_minutes`, `is_realtime`, `status`, `timestamp` and `timezone`. Use it for the frequent refresh against your standard daily quota, and keep `/queue`, `/multi` and `/forecast` (all heavy-class) for when you need `wait_min`, the trend fields or history. Nothing changed in the endpoint itself — only its documentation. It was listed as the "Data Freshness API" and its description mentioned only the freshness rating, never `queue_now`, so it was easy to miss. It is now titled "Live Queue & Freshness API" with the returned fields spelled out. Thanks to the developer who raised this.

---

## 2026-07-20

### Fixed
- **Failed calls now correctly return ok:false** — Some failed requests were returning `HTTP 200` with `ok: true` and the error buried inside `data` — so the documented `if (!ok) throw` pattern could not detect them, and the call was still billed. Affected calls now return `HTTP 400` with `ok: false` and a proper `error.code` / `error.message`, as documented. Seen on `fuel-cities` with an unsupported country and `travel-matrix` with malformed coordinates. Separately, a missing required parameter returned `500 internal_error` instead of `400 bad_request` (an upstream 4xx body was being discarded before its status was read). It now returns `400 bad_request` with the upstream message — e.g. `search` without `?name=`. **Successful responses are byte-for-byte unchanged** — same fields, same params, same quota cost. If your client already branches on `ok`, no change is needed. If it ignored `ok` and read `data` directly, it will now see error envelopes on calls that were always failing.
- **Multi-Checkpoint API: accurate queue data when the cache is cold** — Fixed a bug where `/multi` could return a **wrong queue count** for some checkpoints — mainly Balkan and Hungary–Serbia crossings — whenever its cache was cold. The fallback read a table that, for those crossings, holds no queue data, and reported unrelated values as car counts. Measured examples: a checkpoint with 12 cars reported 6, and several with real queues reported 0. Three changes you may notice: `found: false` now means there is genuinely no recent queue data. Previously you could receive `found: true` with a fabricated `queue_now: 0`.; `wait_status`, `trend_percent` and `trend_direction` are now returned on cold requests — they were `null` before.; The endpoint also falls back when its cached snapshot is stale (older than 24h), not only when it is missing.. No changes to request parameters, quota cost or response shape.
- **Multi-Checkpoint API: fixed double-metering** — Fixed a bug where every `/multi` call was billed twice — once by a generic 1-unit check and again by the endpoint's own variable-cost (N PPIDs × sub-products) formula. Calls now cost exactly ⌈(N×M)/2⌉ units as documented, with no extra charge on top. Also added a Standard/Heavy quota-class badge to each product on the [docs](/en/developers/docs) page, so it's clear at a glance which daily quota an endpoint draws from.

---

## 2026-07-15

### Added
- **Holiday Calendar: country/countries merge, compare_to, multi-lang** — country and countries merged into one param (1-15 comma-separated codes). New compare_to param: same-vs-different holiday comparison across countries, composes with upcoming+days. lang now accepts multiple languages (adds a names object). days=0 or omitted now means no limit in upcoming mode.
- **New product: Holiday Calendar API** — Official public holidays per European country — dates, local names and type. Backed by the same Nager.Date / OpenHolidaysAPI service (with a locally-computed Kosovo calendar) that powers the nakordoni.eu holiday calendar page and the prediction system's calendar factors. `?country=PL&year=2026` — full-year holiday list for one country; `?upcoming=1&days=30` — flat list of upcoming holidays across countries; No params — index of a core country set with each next holiday.

---

## 2026-07-13

### Added
- **New product: Currency Exchange Rates API** — Added the `currency` product — EUR-based exchange rates for PLN, CZK, HUF, USD, GBP, CHF, NOK and UAH, sourced from Frankfurter (ECB) and cached 6 hours. No parameters, always returns the full rate table. See [docs](/en/developers/docs/currency).

---

## 2026-07-12

### Added
- **Free embeddable truck-ban widget** — Embed live European truck driving bans on your own website — a free iframe widget with 3 designs (light, dark, board), 5 languages (en, uk, pl, de, ru), an optional per-country filter and live «active now» status. No API key needed. Configure and copy the code at [nakordoni.eu/en/for_truck_drivers/traffic_bans/widget](https://nakordoni.eu/en/for_truck_drivers/traffic_bans/widget). Prefer raw data? The `truck-bans` API product and the public JSON feed remain available.

---

## 2026-07-11

### Added
- **API v2 (per-endpoint versioning), directional `border`, and an interactive Sandbox** — Three additions, all backward-compatible — **v1 is unchanged**. **Per-endpoint versioning.** There is now an `/api/v2/` base URL. It is per-endpoint: only endpoints that actually changed behave differently under v2; every other endpoint transparently serves its v1 response (so `/api/v2/data/queue` = the same data as v1, just with `"api_version":"v2"`). No need to migrate endpoints that work. **`border` v2 is directional.** The path order is the travel direction: `GET /api/v2/data/border/1/2/6 → buses UA→PL (Ukrainian-side crossings); GET /api/v2/data/border/2/1/6 → buses PL→UA (Polish-side crossings)` Each checkpoint also gains a `direction {from,to}` object and a `stale` boolean, and `?max_age_min=N` returns only recently-updated crossings. (v1 `border` still returns both sides of the border regardless of order — unchanged.) **Interactive Sandbox.** Signed-in developers can now try any endpoint from the browser at [Developers → Sandbox](/en/developers/sandbox) — pick an endpoint, version and one of your keys, tweak parameters and see the live response. Sandbox testing has its own separate daily budget (50 calls/day) and never touches your live API quota. The docs are now split per endpoint ([Developers → API Docs](/en/developers/docs)) with a version selector on endpoints that have more than one version.

---

## 2026-07-10

### Improved
- **`queue-advanced`: two new adjustment factors** — Two new factors layered into the wait-time formula, alongside the existing `section_mode` and weather adjustments: `service_rate` — measured cars/min currently being processed vs the checkpoint's configured baseline rate. Multiplicative, bounded 0.5x-1.5x.; `shift_change` — impact of the checkpoint's own local 08:00/20:00 border-guard shift change. **Additive** (minutes), not multiplicative — only applied within +/-60 minutes of a shift, requires a minimum sample history, clamped to +/-120 minutes.. `advanced_wait_min` is now `round(base_wait × section_mode × weather × service_rate) + shift_change.adjustment_min`. Both factors are also reflected in `driver_reported.prognosed_advanced_wait_min` for historical comparisons.

---

## 2026-07-09

### Breaking
- **Several internal-only fields removed from `queue`, `border`, `multi`, `update-info`** — As part of a security/privacy review, the following fields have been removed — they exposed internal implementation details (our upstream data-source taxonomy, DB row IDs, internal pipeline annotations, unused/dead fields) with no real product value: `id` and `corrected` — removed from `queue` row objects; internal model constants — removed from `queue`, `border` and `multi` (the already-computed `wait_min`/`wait_time` is unaffected); `source` (raw string, e.g. `"line"`) — removed from `queue`, `multi`, and `update-info`. `update-info` and `multi`'s `update_info` block still carry `source_category`/`source_label_en` (a small public vocabulary); `queue` and `multi`'s `queue` block no longer carry any source field at all; `traffic_status` — removed from `border`; it was always `null` and never populated by any part of the system. If your integration reads any of these fields, please update it — see the current field list on the relevant product's docs page.
- **`usage.used` can now be a fractional number** — Daily quota usage (`usage.used` in every response) can now be a decimal value (e.g. `67.5`) instead of always a whole integer. This is a side effect of `queue-advanced` being billed at a fractional rate — see below. `usage.limit` is unaffected and always a whole number. If your client strictly types `usage.used` as an integer, please widen it to accept a decimal/float.
- **`queue-advanced`: billed at 1.5x, response trimmed** — `queue-advanced` now costs **1.5 units** per call instead of 1 (reflecting the extra traffic/weather/driver-report lookups it does) — see `usage.used` above. The response also no longer includes `total_crossing_time`, and `driver_reported` is now just `{wait_min, ts, age_min}` — the previous prognosis-vs-reality comparison fields (`prognosed_wait_min`, `diff_min`, `historical_section_mode`, `historical_weather`, etc.) have been removed. `section_mode`, `weather`, `advanced_wait_min`, and `exceeds_crossing_time` are unchanged.

### Added
- **`wait_status` and `trend_percent`/`trend_direction` added to `border`, `multi`, and `queue-advanced`** — These three products now return the same live-status fields the website shows: `wait_status` (`green`/`yellow`/`red`, based on this checkpoint's own recent history) and `trend_percent`/`trend_direction` (`up`/`up-slight`/`down`/`down-slight`/`stable`, comparing the last 3 hours). Purely additive.

### Improved
- **`queue`: `wait_time` now populated on every historical row** — `/api/v1/data/queue`'s `data[]` rows previously had `wait_time: null` for most sources — only a few upstream feeds report a wait time directly. Rows without one now get our standard estimate, flagged with a new `wait_time_estimated` boolean so you can tell a real reported figure from a computed one.
- **Truck Bans API: live per-country status (`active_window` / `next_window`)** — `/api/v1/data/truck-bans` now returns, for each country in `bans_by_country`, a `status` (`active`/`clear`) plus `active_window`, `next_window`, `local_time` and `tz` — computed in that country's own timezone, so you no longer have to evaluate raw ban windows against a clock yourself. The response also adds a top-level `covered_countries` list and an `as_of` UTC timestamp. `GET /api/v1/data/truck-bans?country=PL` Purely additive — the existing `current_bans`/`upcoming_bans`/`bans_by_country` fields are unchanged. An unknown `?country=` now returns an empty result with `countries_not_covered` instead of every country's bans.

---

## 2026-07-08

### Added
- **New product: Advanced Wait Time API (`queue-advanced`)** — A new opt-in product that adjusts the standard wait time for live traffic flow and weather. Returns the full breakdown of each adjustment. `GET /api/v1/data/queue-advanced?ppid=id_13` **Granted on request** — open a Data ticket from your dashboard to enable it.

### Improved
- **Border Queue API: wait_min now populated for every checkpoint** — `/api/v1/data/border` now correctly computes `wait_min` for every checkpoint in the response, matching the `queue` and `multi` products. Previously this field was always `null`.
- **Forecast API: more consistent model + working weather signal** — `/api/v1/data/forecast` now reliably uses the v4 ensemble model for any `prediction_steps` value (previously some non-standard horizons could silently fall back to an older model). The weather factor that feeds the ensemble is also fixed and now genuinely reflects live conditions (rain, snow, wind, fog) instead of always reporting unavailable.

---

## 2026-07-02

### Added
- **History data export (beta)** — Approved developers can now download hourly-averaged historical border-queue data for up to 5 checkpoints (rolling window up to 90 days) as CSV or NDJSON from the new **Data export** tab. Data is published-only and quality-checked; timestamps are UTC. Need access? Open a Data ticket.

---

## 2026-07-01

### Improved
- **Sign up without a live page — describe your idea instead** — No website yet? You can now create a developer account by describing where and how you plan to use our data, instead of being forced to enter a live page URL. Add the real URL later from your dashboard (Account & data → *Your project*) as soon as your site or app is live — a visible link back to nakordoni.eu on that page is required by our Terms.

---

## 2026-06-22

### Added
- **Submit border news for a dofollow backlink** — Developers can now submit their own border-related news to the Nakordoni news line. If our editors publish it, you get an **indexable dofollow backlink** to your service (publisher byline + source line) and we **translate the article into all 24 languages** for free. **One article per week is free**; additional articles are a paid add-on. Choose *'we may lightly edit + add internal links'* or *'publish as-is'*. Submit and track review status under [Developers → Submit news](/en/developers/news).

---

## 2026-06-14

### Improved
- **Multi-Checkpoint API: 50% quota discount** — The Multi-Checkpoint API (`/api/v1/data/multi`) now bills quota at ⌈(N PPIDs × sub-products) / 2⌉ — half the cost of equivalent individual calls. A request for 10 checkpoints with both sub-products now costs 10 units instead of 20. The `X-Devapi-Units` header and `meta.units_consumed` in the response reflect the discounted amount.

### Added
- **New product: Multi-Checkpoint API (`multi`)** — Fetch live queue status and data freshness for up to 20 checkpoints in a single API call — designed for dashboard builders who currently poll many PPIDs in a loop. Quota counts fairly as **N PPIDs × sub-products** requested, so the total usage is identical to individual calls — but with one round-trip instead of many. GreenTravel-style patterns drop from 24+ calls/hour to 2. `GET /api/v1/data/multi?ppids=id_2,id_13,id_15,id_59&include=queue,update-info&lang=en` `include=queue` — current queue_now, estimated wait_min, data age and checkpoint name; `include=update-info` — data freshness, source classification, age in seconds/minutes; Max 20 PPIDs per request; combine both sub-products in a single call for full dashboard data; Response includes `meta.units_consumed` so you can track quota usage precisely.

---

## 2026-07-03

### Added
- **History data export** — approved developers can download hourly-averaged, published border-queue history for up to **5 checkpoints** (rolling window up to **90 days**) as gzipped **CSV** or **NDJSON**, from the **Data export** tab in the portal. This is a **portal-only** feature — **not** an API endpoint. One row per checkpoint per UTC hour: `ppid, checkpoint_name, hour_utc, direction, vehicle_type, avg_queue_length, avg_wait_minutes, sample_count, source` (`avg_wait_minutes` is `null` where a checkpoint has no official wait feed). Published-only + data-quality filtered; files kept 10 days. Every file embeds a signed provenance fingerprint (`sha256` + `HMAC`) so any copy can be verified as genuine nakordoni.eu data. Access on request via a [Data ticket](https://nakordoni.eu/en/developers/tickets?cat=data). See [`docs/export.md`](docs/export.md).

## 2026-06-12

### Added
- **`queue` product** — `snapshot` block with prognosed wait time (`wait_min`), the same estimate shown on the nakordoni.eu hero section
- **`border` product** — query all checkpoints on a given border + vehicle type in one call
- **`search` product** — find checkpoint PPIDs by name in any of 24 languages
- **`alternatives` product** — `crossing_type` parameter to override vehicle type filter; `limit` parameter

### Improved
- Localized `crossing_type_label` and country names in `checkpoints`, `border`, and `search` responses — all 22 supported languages

---

## 2026-06-05

### Added
- Developer portal launched at https://nakordoni.eu/en/developers
- Initial products: `checkpoints`, `queue`, `stats`, `day-stats`, `forecast`, `alternatives`, `update_info`, `fuel`, `pois`, `truck_bans`, `trading_sundays`, `bus_carriers`, `road_conditions`, `assistant`

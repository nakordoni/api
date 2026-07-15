# Changelog

All notable changes to the Nakordoni Developer API.

See also the live changelog: https://nakordoni.eu/en/developers/changelog

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
- **Several internal-only fields removed from `queue`, `border`, `multi`, `update-info`** — As part of a security/privacy review, the following fields have been removed — they exposed internal implementation details (our upstream data-source taxonomy, DB row IDs, internal pipeline annotations, unused/dead fields) with no real product value: `id` and `corrected` — removed from `queue` row objects; `tmin`/`tpercar` — removed from `queue`, `border`, and `multi` (the wait-time formula constants; the already-computed `wait_min`/`wait_time` is unaffected); `source` (raw string, e.g. `"line"`) — removed from `queue`, `multi`, and `update-info`. `update-info` and `multi`'s `update_info` block still carry `source_category`/`source_label_en` (a small public vocabulary); `queue` and `multi`'s `queue` block no longer carry any source field at all; `traffic_status` — removed from `border`; it was always `null` and never populated by any part of the system. If your integration reads any of these fields, please update it — see the current field list on the relevant product's docs page.
- **`usage.used` can now be a fractional number** — Daily quota usage (`usage.used` in every response) can now be a decimal value (e.g. `67.5`) instead of always a whole integer. This is a side effect of `queue-advanced` being billed at a fractional rate — see below. `usage.limit` is unaffected and always a whole number. If your client strictly types `usage.used` as an integer, please widen it to accept a decimal/float.
- **`queue-advanced`: billed at 1.5x, response trimmed** — `queue-advanced` now costs **1.5 units** per call instead of 1 (reflecting the extra traffic/weather/driver-report lookups it does) — see `usage.used` above. The response also no longer includes `tmin`, `tpercar`, or `total_crossing_time`, and `driver_reported` is now just `{wait_min, ts, age_min}` — the previous prognosis-vs-reality comparison fields (`prognosed_wait_min`, `diff_min`, `historical_section_mode`, `historical_weather`, etc.) have been removed. `section_mode`, `weather`, `advanced_wait_min`, and `exceeds_crossing_time` are unchanged.

### Added
- **`wait_status` and `trend_percent`/`trend_direction` added to `border`, `multi`, and `queue-advanced`** — These three products now return the same live-status fields the website shows: `wait_status` (`green`/`yellow`/`red`, based on this checkpoint's own recent history) and `trend_percent`/`trend_direction` (`up`/`up-slight`/`down`/`down-slight`/`stable`, comparing the last 3 hours). Purely additive.

### Improved
- **`queue`: `wait_time` now populated on every historical row** — `/api/v1/data/queue`'s `data[]` rows previously had `wait_time: null` for most sources — only a few upstream feeds report a wait time directly. Rows without one now get the standard `tmin + queue×tpercar` estimate, flagged with a new `wait_time_estimated` boolean so you can tell a real reported figure from a computed one.
- **Truck Bans API: live per-country status (`active_window` / `next_window`)** — `/api/v1/data/truck-bans` now returns, for each country in `bans_by_country`, a `status` (`active`/`clear`) plus `active_window`, `next_window`, `local_time` and `tz` — computed in that country's own timezone, so you no longer have to evaluate raw ban windows against a clock yourself. The response also adds a top-level `covered_countries` list and an `as_of` UTC timestamp. `GET /api/v1/data/truck-bans?country=PL` Purely additive — the existing `current_bans`/`upcoming_bans`/`bans_by_country` fields are unchanged. An unknown `?country=` now returns an empty result with `countries_not_covered` instead of every country's bans.

---

## 2026-07-08

### Added
- **New product: Advanced Wait Time API (`queue-advanced`)** — A new opt-in product that adjusts the standard wait time for live traffic flow and weather. Returns the full breakdown of each adjustment. `GET /api/v1/data/queue-advanced?ppid=id_13` **Granted on request** — open a Data ticket from your dashboard to enable it.

### Improved
- **Border Queue API: wait_min now populated for every checkpoint** — `/api/v1/data/border` now correctly computes `wait_min` (and returns `tmin`/`tpercar`) for every checkpoint in the response, matching the `queue` and `multi` products. Previously this field was always `null`.
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
- **`queue` product** — `snapshot` block with prognosed wait time (`wait_min = tmin + queue_now × tpercar`), same formula as the nakordoni.eu hero section
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

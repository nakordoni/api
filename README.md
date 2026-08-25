# Nakordoni Developer API

> Real-time border queue data for Europe — live queues, ML forecasts, fuel prices, POIs and more.

**Portal:** https://nakordoni.eu/en/developers
**Docs:** https://nakordoni.eu/en/developers/docs
**Status:** https://nakordoni.eu/en/status
**Changelog:** https://nakordoni.eu/en/developers/changelog
**Raw API reference (for AI assistants):** https://nakordoni.eu/api/v1/docs.md

---

## Quick Start

```bash
curl "https://nakordoni.eu/api/v1/data/queue?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

[Get a free Explorer key →](https://nakordoni.eu/en/developers/signup) (email only, instant, no card)

---

## Products

| Slug | Name | Description | Quota class |
|------|------|-------------|-------------|
| `status` | API Status | Live health of every Developer-API product: online / degraded / offline, response latency and last-checked time, plus an overall rollup. Public — no API key required, never counts against your quota. Refreshed every 5 minutes. Mirrors the human status page at nakordoni.eu/{lang}/status. | cheap |
| `checkpoints` | Checkpoints Directory API | Directory of all monitored border checkpoints: IDs, names, countries, coordinates and status. Use it to discover ppid values for the other APIs. Each row carries has_day_stats — false means the Best Time to Cross API has no matrix for that checkpoint and would answer 404, so skip it instead of polling. | cheap |
| `multi` | Multi-Checkpoint API | Fetch live queue status and/or data freshness for up to 20 checkpoints in a single request. Designed for dashboard builders who poll many PPIDs simultaneously — reduces 20+ individual calls to one. Quota counts as ⌈(N PPIDs × sub-products) / 2⌉ — 50% cheaper than the equivalent individual calls. Use include=queue,update-info to get both datasets at once. | heavy |
| `border` | Border Queue API | All checkpoints on a given border + vehicle type in one call — live queue, wait estimate, and data freshness for every crossing point. Supports single destination, comma-separated list, or "all" to query all neighbours at once. Results sorted by queue length ascending, each checkpoint tagged with its border country. | heavy |
| `search` | Checkpoint Search API | Find checkpoint PPIDs by name. Pass a single name or a comma-separated list (up to 20). Searches all translation languages; returns all PPIDs at that location grouped by vehicle type (4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck). Use this to quickly discover the right ppid before calling the queue or forecast APIs. | cheap |
| `queue` | Live Border Queue API | Real-time queue length, wait estimate and status for any monitored checkpoint, plus hourly/daily aggregates. | heavy |
| `queue-advanced` | Advanced Wait Time API | Wait time adjusted for live traffic flow and weather, with a full breakdown of each adjustment, plus the same wait_status/trend fields as border and multi. Billed at 1.5x a normal heavy-class call, reflecting the extra traffic/weather/driver-report lookups. Granted on request. | heavy |
| `stats` | Checkpoint Hourly Statistics API | Hourly historical queue stats per checkpoint and date: 24 hourly values, daily avg/min/max, peak and quietest hours, day-over-day comparison. | heavy |
| `day-stats` | Best Time to Cross API | Typical-week load statistics per checkpoint: 7×24 day-of-week × hour matrix (median + p25/p75 band), quietest/busiest day, best/worst 2-hour windows. Precomputed daily from ~60 days of real observations. | cheap |
| `forecast` | Queue Forecast API | ML ensemble forecast of queue levels: 24-hour and 7-day (168h) horizons with confidence bounds. The same model that powers nakordoni.eu predictions. | heavy |
| `alternatives` | Checkpoint Alternatives API | Nearby alternative checkpoints on the same border with current queues and distance deltas. By default filters to the same vehicle type as the requested ppid. Use crossing_type to override — e.g. crossing_type=4 for cars, crossing_type=7 for pedestrians. | cheap |
| `update-info` | Live Queue & Freshness API | Current queue length for one checkpoint plus how fresh that reading is — returns queue_now, freshness, age_minutes, is_realtime, status, timestamp and timezone. This is the STANDARD-class way to poll live queue data: use it for frequent refreshes and keep the heavy quota for /queue, /multi and /forecast, which return the fuller payload (wait_min, trend, history). | cheap |
| `fuel` | EU Fuel Prices API | Fuel prices across EU countries — country averages, nearest stations by coordinates, or stations near a border checkpoint. Aggregated from official national price registries and market data. A single-country answer carries a `grades` object mapping each price key (petrol, petrol95plus, petrol92, petrol98, diesel, lpg) to its canonical grade and to the local pump name for that country; the all-countries summary carries the canonical mapping only, as `grade_keys`. | cheap |
| `fuel-cities` | Fuel Prices by City API | Per-city fuel price summary for a country: cheapest station price and average across the top 5 stations in each major city. City-level data today: AT, DE, ES, FR, HR, IT, LU, PT, SI, DK — RO is accepted but has no priced stations yet and returns no cities. Which grades a country can answer differs sharply (SI, IT and PT carry diesel only; HR diesel and lpg; DE, AT, FR and LU carry most of the range), so every response reports `available_fuel_types` derived from the station data actually reachable from that country's cities — read it before choosing `fuel_type`. Asking for a grade the country has no price for returns `cities: []` and a `note` naming what is available; it is never silently answered with a different grade. | cheap |
| `pois` | Driver POIs API | Truck parkings (14k+), free showers, services and supermarkets across Europe with coordinates. Results are sorted closest-first with distance_km. Locate by lat/lon or by city (+country). | cheap |
| `truck-parkings` | Truck Parking API | The closest truck parkings, Autohöfe and truck stops to a point or city — sorted by distance with distance_km, name, coordinates and country. 14k+ locations across Europe. | cheap |
| `shops` | Nearby Shops API | The closest supermarkets and grocery shops to a point or city — sorted by distance with distance_km, name, coordinates and country. | cheap |
| `showers` | Free Showers API | The closest free showers for drivers to a point or city — sorted by distance with distance_km, name, coordinates and country. | cheap |
| `restaurants` | Driver Restaurants API | The closest driver-friendly restaurants to a point or city — sorted by distance with distance_km, name, coordinates and country. | cheap |
| `industrial` | Industrial Zones API | The closest industrial and logistics zones to a point or city (3k+ across Europe) — sorted by distance with distance_km, name, coordinates and country. | cheap |
| `fuel-stations` | Nearby Fuel Stations API | The closest petrol stations to a point or city with current prices per fuel type, sorted by distance. Station-level coverage: DE, IT, FR, AT, ES, SI, HR, LU, PT, DK, plus PL for the Tricity area and the DE/PL border crossings only (Gdansk/Gdynia/Sopot and nearby towns) — same data as the nakordoni.eu fuel pages; use the fuel API country mode for national averages elsewhere. Prices are returned in each station's own currency (PLN for Polish stations, EUR elsewhere) — read the per-station currency field, never compare the raw numbers. When no stations match, the response includes a coverage object naming the covered countries, and a sparse_coverage list of countries covered only in part, instead of a silent empty list. | cheap |
| `internet-points` | Internet Points API | Mobile-operator shops and WiFi points useful to drivers on the road, nearest-first from a point or city with distance_km. | cheap |
| `vignettes` | Vignettes & Road Tolls API | Whether a country requires a vignette for highway travel, current prices per duration and where to read more — per country or for a whole trip. | cheap |
| `fuel-cheapest` | Cheapest Fuel Nearby API | The cheapest petrol stations around a point or city, ranked by price for the chosen fuel type (closest wins a tie). Station-level coverage: DE, IT, FR, AT, ES, SI, HR, LU, PT, DK, plus PL for the Tricity area and the DE/PL border crossings only (Gdansk/Gdynia/Sopot and nearby towns). Ranking is per fuel type within one search area, and prices carry each station's own currency (PLN in Poland, EUR elsewhere). When no stations match, the response includes a coverage object naming the covered countries, and a sparse_coverage list of countries covered only in part, instead of a silent empty list. | cheap |
| `fuel-grades` | Fuel Grade Names API | The naming table behind every fuel product: our canonical grade vocabulary (diesel, premdiesel, truckdiesel, hvo, petrol92, e5, e10, superplus, super100, e85, lpg, cng, lng, adblue) and what each grade is called at a pump in 41 European countries — ON and Olej napędowy in Poland, Nafta in Czechia, Gázolaj in Hungary, Motorină in Romania and Moldova, ДП in Ukraine, Motorin in Turkey, Gasóleo in Portugal and Spain. Every name listed here is accepted by the fuel_type parameter of the fuel, fuel-local, fuel-stations, fuel-cheapest and fuel-cities products, so you can pass a driver's own wording straight through. Resolution is country-first because the same word is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one — send `country` together with a local name. This is a NAMING table and is deliberately wider than our price coverage: it answers what a grade is called in a country whether or not we quote a price there (we hold no station prices for TR or MD at all), and `station_level` on each grade says only which grades station feeds report anywhere. Pass `fuel_type` to probe one name and get back the canonical grade it resolves to, or null when we cannot place it — we never guess a default grade. | cheap |
| `fuel-local` | Local Fuel Price API | Best available fuel price for ANY point in Europe, resolved down a three-tier fallback ladder so a coordinate never comes back empty where we have data at all: pump prices from the nearest stations when the point is inside station-level coverage (DE, IT, FR, AT, ES, SI, HR, LU, PT, DK, plus PL for the Tricity area and the DE/PL border crossings only), else the regional average for the administrative region the point falls in (Ukraine only today — the UA oblast feed, in UAH), else the national average for the country. Every response carries a `resolution` field naming the tier that answered — `station` (data is a list of stations with distance_km, each in its own currency), `region` (one oblast-average object plus `region`, an ISO 3166-2 code such as UA-46, `region_name` and `region_center_dist_km`) or `country` (one national-average object). The region and country tiers return the same price keys: petrol/petrol95plus/petrol92/diesel/lpg + currency + updated date. Branch on `resolution`, never on the shape. Ukraine has no per-station prices anywhere, so a UA point answers at `region` where the oblast is quoted and falls back to `country` otherwise. 404 when the point is outside every covered country (mid-ocean, outside Europe). The region and country tiers also carry a `grades` object mapping each price key to its canonical grade and to the name a driver in that country would use at the pump (UA `diesel` → ДП, PL `petrol` → Pb95). For a plain national average without coordinates use the `fuel` product, for a pure station search `fuel-stations`, for price ranking `fuel-cheapest`. | cheap |
| `currency` | Currency Exchange Rates API | EUR-based exchange rates for PLN, CZK, HUF, USD, GBP, CHF, NOK and UAH — sourced from Frankfurter (ECB), cached 6h. No parameters; always returns the full rate table. Powers the nakordoni.eu currency calculator and fuel-cost pages. | cheap |
| `truck-bans` | Truck Driving Bans API | European truck driving restrictions for one or more countries, including seasonal and holiday bans. Each ban carries its restriction type (General / Local / Sunday / Holiday / Seasonal), the exact scope or roads affected and the minimum weight, and each country a live status computed in its own timezone (active_window / next_window), plus a covered_countries list. ?country= accepts a comma-separated list of up to 3 codes; it is optional in v1 and required in v2. The response also reports `returned`, `total_available` and `truncated` so a capped answer is never mistaken for a complete one, plus `window` {from,to,days} naming the exact date range it covers. v1 always covers the next 7 days; v2 additionally accepts ?date= / ?date_from= + ?date_to= for any window up to 92 days, beginning at most 7 days in the past (this is a forward-looking calendar — older history is refused with 400 date_too_old) and running forward to 2028-12-31. | cheap |
| `trading-sundays` | Trading Sundays API | Sunday retail-opening regulations and upcoming trading Sundays per regulated EU country. | cheap |
| `holiday-calendar` | Holiday Calendar API | Official public holidays per European country — dates, local names and type, each country's full holiday list included. Backed by the same Nager.Date / OpenHolidaysAPI service (7-day cache, locally-computed Kosovo calendar) that powers the nakordoni.eu holiday calendar page and the prediction system's calendar factors. One country -> a flat object; several -> the same shape wrapped in a list. upcoming=1 switches to a flat, date-sorted cross-country feed; compare_to switches to a same-vs-different comparison across countries — useful for checking whether a date is a holiday on both sides of a border crossing or just one. | cheap |
| `bus-carriers` | Bus Carrier Border Stats API | Border-crossing performance per bus carrier: crossings, average/median/min/max wait minutes — built from our own plate-matched crossing records. | cheap |
| `road-conditions` | Road Conditions API | Live road conditions on the approaches to a border crossing: accidents, roadworks, closures, congestion, weather hazards, potholes and ice. Merges our own driver reports with the incident feeds we run the navigator on — national road authorities (GDDKiA, autobahn.de, ÖAMTC, Zjazdnost, Digitraffic, CCISS, DGT) plus TomTom, HERE and crowd reports — deduplicated across sources, each row carrying the `source` it came from. Without coordinates the answer is scoped to the BORDER CORRIDORS rather than a whole country: the last 50 km of the main road from each capital to the crossing, on both sides of the border. Pass ?ppid= for one crossing, or lat/lng+radius for a plain radius search anywhere. | cheap |
| `assistant` | Border AI Assistant API | Ask our production AI assistant any border-crossing question (queues, forecasts, rules, fuel, routes) and get the same grounded answer that powers the nakordoni.eu widget — in 24 languages. Already used in production by yaknakordoni.com.ua. | heavy |
| `assistant-custom` | Personalised AI Assistant API | Your own AI assistant, grounded on YOUR content plus OUR live border data. Point it at your markdown files or let us fetch the pages you name — we index them and answer from them. Choose which of our data feeds it may use (queue, forecast, alternatives, fuel, truck bans, holidays, road conditions…), choose the model tier (that is what sets the price), write your own instructions with {{feed.slug}} placeholders saying exactly where our data goes in the answer, and add a closing sentence of your own that is appended to every reply. Build it and test it at /{lang}/developers/studio, then call it here. Price per answer = model tier units + 1 unit per enabled feed (shown in the studio and in X-Devapi-Units). | heavy |
| `travel-matrix` | Border Travel Matrix API | Travel time + border queue data for all checkpoints from a given origin. Returns drive time, current queue, total estimated journey time, and distance for every relevant crossing — sorted from fastest. Supports multi-leg routes (e.g. Germany → Poland → Ukraine border). Powers the nakordoni.eu navigator "Choose a crossing" feature. | heavy |
| `air-alerts` | Air Alert Proximity API | Air-raid alerts and airborne objects near ONE border crossing. Anchor on a checkpoint (ppid), a coordinate pair or a city and get back only what is inside your radius (default 100 km): which regions are under alert, how far each is, and how close any tracked object currently is on a 1-5 proximity band. Regions are returned as ISO 3166-2 region_code, the same classifier the regional fuel data uses. DELIBERATELY NOT REAL-TIME: every response is a snapshot 1-3 minutes old with the lag re-randomised per request and stated in as_of/lag_min, and object coordinates are never returned - only distance, bearing and speed relative to your anchor. Source data is crowd/OSINT-derived and partly hand-moderated: a signal, never ground truth, and never a substitute for official air-raid warnings. | cheap |
| `route-plan` | Route Planner API | A door-to-door plan for a border trip, not just a driving time. Returns the route, the crossings actually on it with their live or arrival-time-forecast queue, and the stops a driver really makes — rest breaks, a meal, refuelling — laid out on one timeline. The border is part of that timeline: a long queue satisfies the break that was coming up and resets the driving clock, so a 3-hour wait is never reported as 3 hours PLUS a full set of breaks nobody took. Car stops follow a driving-hygiene model; bus and truck stops respect the mandatory EU 561/2006 rest, and the bus service overhead is calibrated on 1000+ licensed international coach schedules. Set stop_places=1 to name a real rest area or fuel station for each stop. Each border also reports wait_basis: vehicle_lane when your vehicle class has its own measured queue, car_lane when the car-lane queue is the best available signal for that crossing. | heavy |
| `fleet-vehicles` | Fleet Vehicles API | Your own NakBus Live fleet: every vehicle registered to your company with plate, label, assigned route, public-map flag, status, whether a driver device is currently on shift, and the last measured timetable delay. Owner-scoped — the key sees its own fleet only, including vehicles hidden from the public bus map. Activate fleet tracking at nakordoni.eu/en/developers/fleet (first bus free). | cheap |
| `fleet-live` | Fleet Live Positions API | Where your buses are right now — one row per active vehicle with lat/lon, timestamp and age, speed, bearing and timetable delay, read from the same live store that powers the public bus map. Unlike the public feed this includes vehicles marked non-public and companies that opted out of the public map: it is your own data. Vehicles whose driver device is off shift (or silent for more than 10 minutes) are still listed, with live=false. | cheap |
| `fleet-history` | Fleet History API | Recorded GPS track of your own vehicles: every stored position point in a time window, in chronological order, with speed, bearing and accuracy. Filter to one vehicle or take the whole fleet. Window is limited to 31 days per call and 5000 points per response — page through longer periods by moving ?from= to the last returned timestamp. | heavy |


**Quota classes** (Explorer free tier): `standard` = 200 req/day · `heavy` = 50 req/day

---

## History data export

Approved developers can download **hourly-averaged, published** border-queue history for up to **5 checkpoints** (rolling window up to **90 days**) as gzipped **CSV**, **NDJSON**, or **JSON**. This is **portal-only** — it is *not* an API endpoint; you build and download exports from the **Data export** tab in your account.

- **Access** is granted on request — open a [Data ticket](https://nakordoni.eu/en/developers/tickets?cat=data) with the checkpoints, time window and intended use. Default limit: 1 export/day, ≤5 checkpoints.
- **Fields** (one row per checkpoint per UTC hour): `ppid, checkpoint_name, hour_utc, direction, vehicle_type, avg_queue_length, avg_wait_minutes, sample_count`. `avg_wait_minutes` is `null` where a checkpoint has no official wait feed.
- **Quality:** published-only data, passed through our anomaly / data-quality checks (no raw per-report data). Files kept 10 days.
- **Provenance:** every file embeds a signed fingerprint (`sha256` + `HMAC`) in its header, so any copy can be confirmed as genuine nakordoni.eu data and checked for tampering — even after download.

Full docs → [`docs/export.md`](docs/export.md) · https://nakordoni.eu/en/developers/docs#export

---

## Authentication

Every request must include:

```
Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX
```

The key is issued instantly after email verification. No credit card required.

---

## Response Envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ],
  "snapshot": { ... },
  "usage": { "requests_today": 12, "limit_today": 200, "remaining": 188 }
}
```

Errors return `ok: false` with `error.code` and the appropriate HTTP status.

---

## Code Samples

**JavaScript**
```js
const res = await fetch('https://nakordoni.eu/api/v1/data/queue?ppid=id_13', {
  headers: { 'Authorization': 'Bearer NKD-DEV-XXXX-XXXX-XXXX' }
});
const { data, snapshot } = await res.json();
console.log('Queue:', snapshot.queue_now, 'cars | Wait:', snapshot.wait_min, 'min');
```

**Python**
```python
import os, requests
KEY = os.environ["NKD_DEV_KEY"]
r = requests.get("https://nakordoni.eu/api/v1/data/queue",
                 params={"ppid": "id_13"},
                 headers={"Authorization": f"Bearer {KEY}"})
snap = r.json()["snapshot"]
print(f"Queue: {snap['queue_now']} cars | Wait: {snap['wait_min']} min")
```

**PHP**
```php
$ch = curl_init("https://nakordoni.eu/api/v1/data/queue?ppid=id_13");
curl_setopt($ch, CURLOPT_HTTPHEADER, ["Authorization: Bearer " . getenv("NKD_DEV_KEY")]);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$r = json_decode(curl_exec($ch), true);
echo $r["snapshot"]["queue_now"] . " cars in queue";
```

---

## Reference

### Country codes
`1`=UA `2`=PL `3`=SK `4`=HU `5`=RO `6`=MD `7`=BY `8`=LT `9`=LV
`10`=EE `11`=SI `12`=BG `13`=RS `14`=TR `18`=DE `19`=GR `21`=AL `23`=XK

### Vehicle / crossing types
`4`=Car UA→EU · `5`=Car EU→UA · `6`=Bus · `7`=Pedestrian · `8`=Truck <7.5t · `9`=Truck

### PPID format
Checkpoints are identified by `id_NNN` (e.g. `id_13` = Rava-Ruska UA/PL car crossing).
Use the [`checkpoints`](docs/checkpoints.md) or [`search`](docs/search.md) product to discover PPIDs.

---

## Attribution

Explorer-plan integrations must display a visible "Data by nakordoni.eu" link on the page where data appears:

```html
<a href="https://nakordoni.eu" rel="noopener">Data by nakordoni.eu</a>
```

Pay As You Grow customers may omit attribution.

---

## Per-product docs

See the [`docs/`](docs/) directory for one file per product with endpoint details, parameters and example responses.

OpenAPI 3.0 spec: [`openapi.yaml`](openapi.yaml)

---

*Last updated: 2026-08-25*
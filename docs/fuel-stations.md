# Nearby Fuel Stations API

The closest petrol stations to a point or city with current prices per fuel type, sorted by distance. Station-level coverage spans 39 European countries and is MEASURED, not fixed: every empty answer returns the current country list with per-country station counts in its coverage object, scoped to the grade you asked for, so read that rather than a list in this description — same data as the nakordoni.eu fuel pages; use the fuel API country mode for national averages elsewhere. Prices are returned in each station's own currency (PLN for Polish stations, EUR elsewhere) — read the per-station currency field, never compare the raw numbers. When no stations match, the response includes a coverage object instead of a silent empty list: station_countries and station_counts measured from the live index (scoped to `grade` when you named a fuel_type), sparse_coverage for the countries holding 25 priced stations or fewer, measured_at, and a fuel_type_note that separates the two reasons an answer can be empty — a grade name we could not place, versus a grade we recognise but do not price in that country.

**Endpoint:** `GET /api/v2/data/fuel-stations`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius_km` | Search radius km (default 25, max 25; alias: radius) |
| `fuel_type` | Optional filter: diesel \| e5 \| e10 \| superplus \| super100 \| premdiesel \| truckdiesel \| hvo \| lpg \| cng \| adblue \| e85 \| lng (availability varies by station/region; Polish stations report Pb95 as e5, Pb98 as superplus and ON as diesel). Local pump names are accepted as well — ON, Olej napędowy, Pb95, Nafta, Dizel, Gázolaj, Motorină, Benzină 95, ДП, А-95, Motorin, Gasóleo, Sans plomb 95, Bleifrei … — resolved against `country` (or, for coordinate products, the country the point falls in), because the same wording is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one. The response echoes `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade — the answer comes back empty and says so. Full table of names per country: /api/v2/data/fuel-grades. |
| `limit` | Max stations (default 5, max 20) |
| `lang` | Language for labels (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fuel-stations?city=Munich&country=DE&radius_km=20&fuel_type=diesel" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.anchor.lat` | The point actually searched (after city geocoding), with lng, ppid and radius_km. |
| `data.anchor.fuel_type` | The canonical grade the list was filtered to; fuel_type_requested is what you sent, fuel_type_local the local pump name and fuel_type_country the country the name was read in. |
| `data.stations[].name` | Station, with brand and address. Each physical station appears ONCE: its grades are nested under prices, not spread over repeated rows. `brand` is the chain when we know it and null when we do not — never a placeholder token, so treat null as "independent or unknown" and fall back to `name`. Address detail varies by national feed: some report a full street address, others only the town. |
| `data.stations[].station_ref` | Stable key for the station (name + position). Key your records on it whenever id is null — id is set only for rows that come from our station directory. |
| `data.stations[].prices` | Object keyed by grade (diesel, e5, e10, lpg …): price, currency and currency_symbol, local_name (the pump name used in that country), label, updated_at, confirmed_at, age_hours and stale. Each station carries its own currency, so never compare the raw numbers. A station with no quote at all carries an empty object here. TWO CLOCKS, and they answer different questions — do not derive one from the other: `updated_at` is when this price last MOVED, so a forecourt that has held the same price for ten days keeps a ten-day-old stamp and that is correct, not stale data; `confirmed_at` is when we last CONFIRMED the quote against its feed, and it is the clock `age_hours` and `stale` are measured on (age_hours = now − confirmed_at). Read `confirmed_at`/`stale` for freshness and `updated_at` for price history. Both are floored to the hour, so `now − confirmed_at` can sit up to an hour above `age_hours`, which is measured before the floor; `confirmed_at` is never earlier than `updated_at`, because a price change is itself a confirmation. |
| `data.stations[].grades` | The grade keys present in prices, alongside freshest_age_hours and a station-level stale flag (true only when NO quote of that station is current). |
| `data.stations[].distance_km` | Distance from the anchor, with lat and lng of the station itself. |
| `data.count` | Stations returned; data.total_found is how many matched inside the radius before limit was applied. |
| `data.notices` | Present whenever we adjusted a parameter you sent: each entry names the param, the requested and the applied value, and the reason. |
| `data.coverage` | Only when nothing matched. grade (the fuel_type your wording resolved to, or null), station_countries and station_counts measured from the live station index and scoped to that grade, sparse_coverage for countries with 25 priced stations or fewer, measured_at, a note explaining all of it, and fuel_type_note when the grade name could not be placed OR when we recognise the grade but price it nowhere in this country. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fuel-stations",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fuel-stations
*Auto-generated 2026-09-22 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
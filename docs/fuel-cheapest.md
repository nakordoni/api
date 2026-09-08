# Cheapest Fuel Nearby API

The cheapest petrol stations around a point or city, ranked by price for the chosen fuel type (closest wins a tie). Station-level coverage spans 39 European countries and is MEASURED, not fixed: every empty answer returns the current country list with per-country station counts in its coverage object, scoped to the grade you asked for, so read that rather than a list in this description. Ranking is per fuel type within one search area, and prices carry each station's own currency (PLN in Poland, EUR elsewhere). When no stations match, the response includes a coverage object instead of a silent empty list: station_countries and station_counts measured from the live index (scoped to `grade` when you named a fuel_type), sparse_coverage for the countries holding 25 priced stations or fewer, measured_at, and a fuel_type_note that separates the two reasons an answer can be empty — a grade name we could not place, versus a grade we recognise but do not price in that country.

**Endpoint:** `GET /api/v2/data/fuel-cheapest`
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
| `fuel_type` | diesel (default) \| e5 \| e10 \| superplus \| super100 \| premdiesel \| truckdiesel \| hvo \| lpg \| cng \| adblue \| e85 \| lng — price ranking is per fuel type. Local pump names are accepted as well — ON, Olej napędowy, Pb95, Nafta, Dizel, Gázolaj, Motorină, Benzină 95, ДП, А-95, Motorin, Gasóleo, Sans plomb 95, Bleifrei … — resolved against `country` (or, for coordinate products, the country the point falls in), because the same wording is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one. The response echoes `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade — the answer comes back empty and says so. Full table of names per country: /api/v2/data/fuel-grades. |
| `limit` | Max stations (default 5, max 20) |
| `lang` | Language for labels (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fuel-cheapest?city=Munich&country=DE&radius_km=25" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.stations[]` | The same station objects as fuel-stations (grades nested under prices, one entry per physical station), ranked cheapest first for the chosen grade — closest wins a tie, and a station without a quote for that grade ranks last. |
| `data.anchor` | The point searched and the resolved grade: lat, lng, radius_km, fuel_type, fuel_type_requested, fuel_type_local, fuel_type_country. |
| `data.count` | Stations returned; data.total_found is how many matched inside the radius before limit was applied. |
| `data.coverage` | Only when nothing matched — grade, station_countries + station_counts measured from the live index (scoped to that grade), sparse_coverage, measured_at, and fuel_type_note for both an unplaceable grade name and a recognised grade we do not price in this country. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fuel-cheapest",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fuel-cheapest
*Auto-generated 2026-09-08 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
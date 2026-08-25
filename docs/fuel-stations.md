# Nearby Fuel Stations API

The closest petrol stations to a point or city with current prices per fuel type, sorted by distance. Station-level coverage: DE, IT, FR, AT, ES, SI, HR, LU, PT, DK, plus PL for the Tricity area and the DE/PL border crossings only (Gdansk/Gdynia/Sopot and nearby towns) — same data as the nakordoni.eu fuel pages; use the fuel API country mode for national averages elsewhere. Prices are returned in each station's own currency (PLN for Polish stations, EUR elsewhere) — read the per-station currency field, never compare the raw numbers. When no stations match, the response includes a coverage object naming the covered countries, and a sparse_coverage list of countries covered only in part, instead of a silent empty list.

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
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.anchor.lat` | The point actually searched (after city geocoding), with lng, ppid and radius_km. |
| `data.anchor.fuel_type` | The canonical grade the list was filtered to; fuel_type_requested is what you sent, fuel_type_local the local pump name and fuel_type_country the country the name was read in. |
| `data.data[].name` | Station, with brand and address. |
| `data.data[].price` | Price for that grade in data.data[].currency — each station carries its own currency, so never compare the raw numbers. |
| `data.data[].fuel_type` | Grade this price is for; a station appears once per grade. |
| `data.data[].distance_km` | Distance from the anchor, with lat and lng of the station itself. |
| `data.data[].updated_at` | When the price was last seen. |
| `data.count` | Stations returned. |
| `data.coverage` | Only when nothing matched: station_countries we cover, sparse_coverage for countries covered only in part, a note explaining both, and fuel_type_note when the grade name itself could not be placed. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
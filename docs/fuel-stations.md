# Nearby Fuel Stations API

The closest petrol stations to a point or city with current prices per fuel type, sorted by distance. Station-level coverage: DE, IT, FR, AT, ES, SI, HR, LU, PT, DK (same data as the nakordoni.eu fuel pages; use the fuel API country mode for national averages elsewhere). When no stations match, the response includes a coverage object naming the covered countries instead of a silent empty list.

**Endpoint:** `GET /api/v1/data/fuel-stations`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius_km` | Search radius km (default 30, max 150; alias: radius) |
| `fuel_type` | Optional filter: diesel | e5 | e10 | superplus | super100 | premdiesel | truckdiesel | hvo | lpg | cng | adblue | e85 (availability varies by station/region) |
| `limit` | Max stations (default 5, max 20) |
| `lang` | Language for labels (default en) |


## Example

```bash
curl "/api/v2/data/fuel-stations?city=Munich&country=DE&radius_km=20&fuel_type=diesel" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

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
*Auto-generated 2026-08-19 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
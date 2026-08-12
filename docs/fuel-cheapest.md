# Cheapest Fuel Nearby API

The cheapest petrol stations around a point or city, ranked by price for the chosen fuel type (closest wins a tie). Station-level coverage: DE, IT, FR, AT, ES, SI, HR, LU, PT, DK.

**Endpoint:** `GET /api/v1/data/fuel-cheapest`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius_km` | Search radius km (default 30, max 150) |
| `fuel_type` | diesel (default) | e5 | e10 | lpg — price ranking is per fuel type |
| `limit` | Max stations (default 5, max 20) |
| `lang` | Language for labels (default en) |


## Example

```bash
curl "/api/v2/data/fuel-cheapest?city=Munich&country=DE&radius_km=25" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

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
*Auto-generated 2026-08-12 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
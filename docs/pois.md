# Driver POIs API

Truck parkings (14k+), free showers, services and supermarkets across Europe with coordinates. Results are sorted closest-first with distance_km. Locate by lat/lon or by city (+country).

**Endpoint:** `GET /api/v1/data/pois`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `type` | parking|autohof|truck_stop|shower|restaurant|supermarket|industrial (comma-separable) |
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city (e.g. city=Brest needs FR vs BY) |
| `radius` | Radius km (default 25, max 150) |
| `limit` | Max results (default 50, max 500) |
| `lang` | Language for country names (default en) |


## Example

```bash
curl "/api/v1/data/pois?type=parking&lat=50.7&lon=23.9&radius=50" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "pois",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#pois
*Auto-generated 2026-08-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
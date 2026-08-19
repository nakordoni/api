# Truck Parking API

The closest truck parkings, Autohöfe and truck stops to a point or city — sorted by distance with distance_km, name, coordinates and country. 14k+ locations across Europe.

**Endpoint:** `GET /api/v1/data/truck-parkings`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius` | Radius km (default 25, max 150) |
| `limit` | Max results (default 50, max 500) |
| `lang` | Language for country names (default en) |


## Example

```bash
curl "/api/v2/data/truck-parkings?city=Katowice&country=PL&radius=40" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "truck-parkings",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#truck-parkings
*Auto-generated 2026-08-19 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
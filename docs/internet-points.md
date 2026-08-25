# Internet Points API

Mobile-operator shops and WiFi points useful to drivers on the road, nearest-first from a point or city with distance_km.

**Endpoint:** `GET /api/v2/data/internet-points`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `limit` | Max results (default 20, max 50) |
| `lang` | Language for country names (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/internet-points?lat=52.2&lon=21.0" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.points[].name` | Public internet point, with description and id. |
| `data.points[].lat` | Coordinates, with lng, distance_km, country and country_name. |
| `data.total_found` | Matches inside the radius; search_location echoes the point searched. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "internet-points",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#internet-points
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Internet Points API

Mobile-operator shops and WiFi points useful to drivers on the road, nearest-first from a point or city with distance_km.

**Endpoint:** `GET /api/v1/data/internet-points`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

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
curl "/api/v2/data/internet-points?lat=52.2&lon=21.0" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

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
*Auto-generated 2026-08-19 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
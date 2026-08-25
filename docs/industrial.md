# Industrial Zones API

The closest industrial and logistics zones to a point or city (3k+ across Europe) — sorted by distance with distance_km, name, coordinates and country.

**Endpoint:** `GET /api/v2/data/industrial`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

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
curl "https://nakordoni.eu/api/v2/data/industrial?city=Duisburg&country=DE&radius=40" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.pois[]` | Industrial and logistics zones near the point: id, type, name, address, lat, lng, country, country_code, country_name, distance_km. |
| `data.total_found` | Matches inside the radius, with limit and search_location. |
| `resolved_location` | Envelope level: the coordinates a city query resolved to. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "industrial",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#industrial
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
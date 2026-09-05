# Truck Parking API

The closest truck parkings, Autohöfe and truck stops to a point or city — sorted by distance with distance_km, name, coordinates and country. 14k+ locations across Europe.

**Endpoint:** `GET /api/v2/data/truck-parkings`
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
curl "https://nakordoni.eu/api/v2/data/truck-parkings?city=Katowice&country=PL&radius=40" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.pois[]` | Truck parkings, autohofs and truck stops near the point: id, type, name, address, lat, lng, country, country_code, country_name, distance_km. |
| `data.total_found` | Matches inside the radius, with limit and search_location. |
| `resolved_location` | Envelope level: when you passed a city, the coordinates it resolved to — query, lat, lon, label. |


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
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
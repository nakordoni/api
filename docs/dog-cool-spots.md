# Cool Spots for Dogs API

Places to cool a dog down near a point: safe water access on lake and river shores, bathing places, drinking water, forests, all-day shaded patches of grass or footway, dog parks and dog-friendly indoor places. Each spot comes with walking distance, shade now and over the next 2 hours, nearby water, surface and a score. Shade coverage: Poznan (PL); elsewhere in the covered OSM region spots come without shade (shade_unavailable).

**Endpoint:** `GET /api/v2/data/dog-cool-spots`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude (required) |
| `lon` | Longitude (required) |
| `radius_m` | Search radius in metres, 100-5000 (default 1500) |
| `types` | Comma list of water_access, drinking_water, forest, shade_allday, dog_park, indoor (default all) |
| `t` | Local ISO time (default now) |
| `sort` | distance (default) \| score |
| `limit` | Max spots, 1-60 (default 25) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/dog-cool-spots?lat=52.4083&lon=16.9335&t=2026-07-15T13:00" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `coverage` | shade active \| unavailable, city and OSM region used. |
| `context` | Local time, sun position, weather (temp_c, cloud_pct, heat level). |
| `spots[]` | type, name, lat, lon, distance_m, walk_m, walk_min, shade_now (0..1), has_water, water_m, surface (grass, forest, paved, unpaved, sand, indoor), details. |
| `spots[].shade_next` | Shade every 30 min for the next 2 hours. Not on the Explorer plan. |
| `spots[].score` | 0-100 cool-spot score. Not on the Explorer plan. |
| `spots[].reasons` | Reason keys (shaded_now, water_nearby, all_day_shade, ...). Not on the Explorer plan. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "dog-cool-spots",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#dog-cool-spots
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
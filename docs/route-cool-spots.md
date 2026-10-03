# Cool Spots Along a Route API

Truck-accessible stops within 2 km of a route you already have: truck parkings shaded by trees (canopy share within 100 m), rest areas with restaurant, motel or showers, and drinking water. Each spot comes with its position along the route, ETA, detour estimate, weather and sun at the ETA and a heat-aware score. Europe-wide.

**Endpoint:** `GET /api/v2/data/route-cool-spots`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `polyline6` | Route geometry, encoded polyline precision 6 (required). Simplify to ~100 m first so the URL stays under 8 KB (about 2.5 KB for 800 km) |
| `depart` | Departure, ISO 8601 with offset or unix (default now) |
| `avg_speed_kmh` | Average speed for the ETA (default 70) |
| `types` | Comma list of parking_shade, rest_ac, drinking_water (default all) |
| `from_km` | Only spots at or beyond this distance along the route (default 0) |
| `corridor_km` | Max distance off the route, 0.2-2 (default 2) |
| `limit` | Max spots, 1-200 (default 60) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/route-cool-spots?polyline6=<encoded>&depart=2026-07-15T13:00:00%2B02:00&types=parking_shade,rest_ac" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `summary` | route_km, from_km, corridor_km, types, spots_total, returned, by_type, depart, arrive, data_built; best (not on Explorer). |
| `spots[]` | type, types, kind, name, country, lat, lon, at_km, eta, off_route_m, detour_km, canopy_pct, amenities, water_m, capacity; weather (temp_c, cloud_pct, rain, sun_elevation at ETA), score 0-100 and reasons — not on the Explorer plan. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "route-cool-spots",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#route-cool-spots
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Cool Spots for Dogs Along a Walk API

Cool spots for dogs along a walking route: every spot within the corridor with its position along the walk and the shade at the time you reach it, plus the best cool stop around the middle of the walk. Pass the route as an encoded polyline (precision 6). Shade coverage: Poznan (PL).

**Endpoint:** `GET /api/v2/data/dog-cool-spots-route`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `polyline6` | Walk geometry, encoded polyline precision 6 (required) |
| `depart` | Departure, local ISO time (default now) |
| `speed_kmh` | Walking speed 2-8 km/h (default 4.5) |
| `corridor_m` | Corridor half-width in metres, 20-300 (default 150) |
| `types` | Comma list of spot types (default all) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/dog-cool-spots-route?polyline6=...&depart=2026-07-15T13:00" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `route_m` | Walk length in metres. |
| `best_stop` | Best spot in the middle 40 % of the walk (up to 300 m off the line): distance_from_route_m, at_m, eta_local, shade_now, has_water. |
| `corridor_spots[]` | Up to 30 spots within corridor_m: distance_from_route_m, at_m, eta_local, type, name, shade_now, has_water, surface. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "dog-cool-spots-route",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#dog-cool-spots-route
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
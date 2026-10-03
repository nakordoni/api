# Shade Walking Route API

Pedestrian routes ranked by how much of the way lies in building and tree shade at the time of the walk (heat) or in sun (cold). Each route comes with distance, duration, shade share, an asphalt surface temperature estimate and per-segment shade as GeoJSON, plus the best departure window in the next 6 hours. Pilot coverage: Poznan (PL); elsewhere plain routes with shade_routing=unavailable.

**Endpoint:** `GET /api/v2/data/shade-route`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `from` | Start as lat,lon (required) |
| `to` | Destination as lat,lon — omit and pass loop_km for a round walk |
| `loop_km` | Round-walk length in km (1-15) when to is omitted |
| `depart` | Local ISO time of the walk, e.g. 2026-07-15T12:00 (default now) |
| `mode` | auto \| heat \| cold (default auto) |
| `alternatives` | Extra routes beyond the best and the direct one, 0-4 (default 2) |
| `segments` | 0 to omit per-segment GeoJSON (summary only) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/shade-route?from=52.4013,16.9115&to=52.4084,16.9336&depart=2026-07-15T12:00" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `context.shade_routing` | active \| suspended (cloud >= 70 %, ranked by length only) \| night (sun below 3 deg) \| unavailable (outside the covered cities or no data for that date). |
| `context.mode_resolved` | heat (prefer shade) \| cold (prefer sun) \| neutral, resolved from mode=auto by temperature and UV. |
| `context.sun` | Sun azimuth and elevation in degrees at the requested time. |
| `context.weather` | temp_c, uv_index, cloud_pct for the hour of the walk. |
| `routes[].id` | cool (most shade for its length) / sunny (cold mode) / direct / alt1.. |
| `routes[].summary` | km, min, shade_pct, sun_exposure_pct, peak_pavement_c (estimate), shade_confidence 0..1. |
| `routes[].readiness` | go \| caution \| wait. |
| `routes[].segments` | GeoJSON FeatureCollection of ~50 m LineStrings with shade, sun_exposure, pavement_c. Not on the Explorer plan (summary only). |
| `best_window` | Best 15-min departure in the next 6 h for the first route: time_local, shade_pct, peak_pavement_c, readiness. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "shade-route",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#shade-route
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
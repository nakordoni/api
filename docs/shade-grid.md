# Shade Grid API

Shade per H3 resolution-11 hex (about 25 m) for a small bounding box at a given time: share of open ground in building shade and in tree shade, and the combined value. Max 4 km2 / 3000 hexes per call. Pilot coverage: Poznan (PL).

**Endpoint:** `GET /api/v2/data/shade-grid`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `bbox` | west,south,east,north (required, max 4 km2) |
| `t` | Local ISO time (default now) |
| `mode` | auto \| heat \| cold |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/shade-grid?bbox=16.918,52.405,16.928,52.410&t=2026-07-15T12:00" \
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
| `summary` | hexes, mean_shade, shaded_share (hexes with shade >= 0.5), mean_sun_exposure. |
| `grid` | GeoJSON FeatureCollection of hex polygons: h3, shade, building, tree, sun_exposure (0..1), open_ground, height_confidence. Not on the Explorer plan (summary only). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "shade-grid",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#shade-grid
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
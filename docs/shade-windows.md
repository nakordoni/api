# Shade Walk Windows API

Shade share and pavement temperature estimate of one walk for every 15-minute departure in the next hours, and the best window. Pass the walk as an encoded polyline (precision 6) or as from + to / loop_km. Pilot coverage: Poznan (PL).

**Endpoint:** `GET /api/v2/data/shade-windows`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `polyline6` | Walk geometry, encoded polyline precision 6 |
| `from` | Start lat,lon (when no polyline6) |
| `to` | Destination lat,lon |
| `loop_km` | Round-walk length in km |
| `depart` | First departure, local ISO time (default now) |
| `hours` | Hours to scan, 1-12 (default 6) |
| `mode` | auto \| heat \| cold |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/shade-windows?from=52.3995,16.9019&loop_km=3" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `shade_routing` | active \| unavailable. |
| `windows[]` | time_local, shade_routing, sun_elevation, shade_pct, sun_exposure_pct, temp_c, peak_pavement_c, readiness. |
| `best_window` | The best entry of windows[]. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "shade-windows",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#shade-windows
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
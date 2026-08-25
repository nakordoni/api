# Fleet Live Positions API

Where your buses are right now — one row per active vehicle with lat/lon, timestamp and age, speed, bearing and timetable delay, read from the same live store that powers the public bus map. Unlike the public feed this includes vehicles marked non-public and companies that opted out of the public map: it is your own data. Vehicles whose driver device is off shift (or silent for more than 10 minutes) are still listed, with live=false.

**Endpoint:** `GET /api/v2/data/fleet-live`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fleet/live" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.vehicles[].vehicle_id` | Vehicle, with plate and label. |
| `data.vehicles[].lat` | Last known position, with lon, ts, datetime and age_s — how old the fix is. |
| `data.vehicles[].live` | false = the position is stale, not moving; speed_ms and bearing_deg describe the fix itself. |
| `data.vehicles[].delay_min` | Current delay against the schedule, with route_id and public. |
| `data.count` | Vehicles returned, with live_count — how many have a fresh fix. ts is the snapshot time and source names the feed. |
| `data.company` | Your fleet company: company_id, name, status, public_map. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fleet-live",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fleet-live
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
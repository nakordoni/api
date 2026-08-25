# Fleet History API

Recorded GPS track of your own vehicles: every stored position point in a time window, in chronological order, with speed, bearing and accuracy. Filter to one vehicle or take the whole fleet. Window is limited to 31 days per call and 5000 points per response — page through longer periods by moving ?from= to the last returned timestamp.

**Endpoint:** `GET /api/v2/data/fleet-history`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `vehicle_id` | Restrict to one of your vehicles (optional; ids come from fleet/vehicles). Omit for the whole fleet. |
| `from` | Window start — unix timestamp or ISO-8601 datetime (default: 24 h before `to`). A datetime without an explicit offset is read as UTC. |
| `to` | Window end — unix timestamp or ISO-8601 datetime (default: now). The window may not exceed 31 days. |
| `limit` | Max points returned, 1-5000 (default 1000, values above 5000 are clamped). `truncated: true` means more points exist in the window. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fleet/history?vehicle_id=1&from=2026-08-01T00:00:00Z&to=2026-08-02T00:00:00Z&limit=1000" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.points[].vehicle_id` | Vehicle the fix belongs to, with plate. |
| `data.points[].lat` | Position, with lon, ts, datetime (UTC) and accuracy_m. |
| `data.points[].speed_ms` | Speed in m/s at the fix, with bearing_deg. |
| `data.from` | Window actually queried, with to and limit. |
| `data.count` | Points returned; truncated is true when count reached limit — continue from the last ts rather than widening the window. |
| `data.vehicle_id` | The vehicle filtered to, null when the window covers the whole fleet. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fleet-history",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fleet-history
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Fleet Live Positions API

Where your buses are right now — one row per active vehicle with lat/lon, timestamp and age, speed, bearing and timetable delay, read from the same live store that powers the public bus map. Unlike the public feed this includes vehicles marked non-public and companies that opted out of the public map: it is your own data. Vehicles whose driver device is off shift (or silent for more than 10 minutes) are still listed, with live=false.

**Endpoint:** `GET /api/v1/data/fleet-live`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "/api/v2/data/fleet/live" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

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
*Auto-generated 2026-08-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Road Conditions API

Approved road condition reports near borders and on major corridors: potholes, roadworks, closures, ice, hazards — combining driver reports with automatic accelerometer detections from our navigation app.

**Endpoint:** `GET /api/v1/data/road-conditions`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO country code (optional) |
| `condition_type` | pothole|speed_bump|roadwork|closure|hazard|ice|… (optional) |
| `severity` | low|moderate|major|critical (optional) |
| `lat` | Latitude (optional) |
| `lng` | Longitude (optional) |
| `radius` | Radius km (default 50) |
| `limit` | Max results (default 100, cap 500) |


## Example

```bash
curl "/api/v1/data/road-conditions?country=UA&severity=major" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "road-conditions",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#road-conditions
*Auto-generated 2026-06-23 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
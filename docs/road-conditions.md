# Road Conditions API

Approved road condition reports near borders and on major corridors: potholes, roadworks, closures, ice, hazards — combining driver reports with automatic accelerometer detections from our navigation app.

**Endpoint:** `GET /api/v1/data/road-conditions?country=UA&severity=major`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `country` | ISO country code (optional) |
| `condition_type` | pothole|speed_bump|roadwork|closure|hazard|ice|… (optional) |
| `severity` | low|moderate|major|critical (optional) |
| `lat` | Latitude (optional) |
| `lng` | Longitude (optional) |
| `radius` | Radius km (default 50) |
| `limit` | Max results (default 100, cap 500) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/road-conditions?country=UA&severity=major" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
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

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#road-conditions)

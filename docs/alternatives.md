# Checkpoint Alternatives API

Nearby alternative checkpoints on the same border with current queues and distance deltas. By default filters to the same vehicle type as the requested ppid. Use crossing_type to override — e.g. crossing_type=4 for cars, crossing_type=7 for pedestrians.

**Endpoint:** `GET /api/v1/data/alternatives`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID (e.g. id_13) |
| `lang` | Language code (default uk) |
| `crossing_type` | Optional vehicle type override: 4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck. Defaults to same type as the requested ppid. |
| `limit` | Max results (1–10, default 5) |


## Example

```bash
curl "/api/v1/data/alternatives?ppid=id_13&crossing_type=4" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "alternatives",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#alternatives
*Auto-generated 2026-07-20 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
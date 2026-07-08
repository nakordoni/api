# Advanced Wait Time API

Wait time adjusted for live traffic flow and weather, with a full breakdown of each adjustment.

**Endpoint:** `GET /api/v1/data/queue-advanced`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |


## Example

```bash
curl "/api/v1/data/queue-advanced?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue-advanced",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#queue-advanced
*Auto-generated 2026-07-08 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
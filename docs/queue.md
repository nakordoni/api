# Live Border Queue API

Real-time queue length, wait estimate and status for any monitored checkpoint, plus hourly/daily aggregates.

**Endpoint:** `GET /api/v1/data/queue`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `section` | Data section (optional) |
| `origin` | Origin country code (optional) |
| `destination` | Destination country code (optional) |


## Example

```bash
curl "/api/v1/data/queue?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#queue
*Auto-generated 2026-07-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
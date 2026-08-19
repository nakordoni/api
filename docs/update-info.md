# Live Queue & Freshness API

Current queue length for one checkpoint plus how fresh that reading is — returns queue_now, freshness, age_minutes, is_realtime, status, timestamp and timezone. This is the STANDARD-class way to poll live queue data: use it for frequent refreshes and keep the heavy quota for /queue, /multi and /forecast, which return the fuller payload (wait_min, trend, history).

**Endpoint:** `GET /api/v1/data/update-info`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `lang` | Language code |


## Example

```bash
curl "/api/v1/data/update-info?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "update-info",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#update-info
*Auto-generated 2026-08-19 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Checkpoint Hourly Statistics API

Hourly historical queue stats per checkpoint and date: 24 hourly values, daily avg/min/max, peak and quietest hours, day-over-day comparison.

**Endpoint:** `GET /api/v1/data/stats`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID |
| `date` | YYYY-MM-DD (default: yesterday) |
| `compare` | 1 = include previous day + delta |
| `lang` | Language code (default uk) |


## Example

```bash
curl "/api/v1/data/stats?ppid=id_15&date=2026-06-01&compare=1" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "stats",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#stats
*Auto-generated 2026-07-08 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
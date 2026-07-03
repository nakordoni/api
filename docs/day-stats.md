# Best Time to Cross API

Typical-week load statistics per checkpoint: 7×24 day-of-week × hour matrix (median + p25/p75 band), quietest/busiest day, best/worst 2-hour windows. Precomputed daily from ~60 days of real observations.

**Endpoint:** `GET /api/v1/data/day-stats`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 |
| `lang` | Language for weekday/unit labels (default uk) |


## Example

```bash
curl "/api/v1/data/day-stats?ppid=id_13&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "day-stats",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#day-stats
*Auto-generated 2026-07-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# API Status

Live health of every Developer-API product: online / degraded / offline, response latency and last-checked time, plus an overall rollup. Public — no API key required, never counts against your quota. Refreshed every 5 minutes. Mirrors the human status page at nakordoni.eu/{lang}/status.

**Endpoint:** `GET /api/v1/data/status`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "/api/v1/data/status" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "status",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#status
*Auto-generated 2026-07-12 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
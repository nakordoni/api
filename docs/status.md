# API Status

Live health of every Developer-API product: online / degraded / offline, response latency and last-checked time, plus an overall rollup. Public — no API key required, never counts against your quota. Refreshed every 5 minutes. Mirrors the human status page at nakordoni.eu/{lang}/status.

**Endpoint:** `GET /api/v1/data/status`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "https://nakordoni.eu/api/v1/data/status" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.checked_at` | When the health snapshot was taken, ISO-8601 UTC. Refreshed every 5 minutes — not a live probe of the moment you call. |
| `data.overall` | Rollup across every product: operational \| degraded \| outage. |
| `data.summary` | Counts behind the rollup: total, online, degraded, offline. |
| `data.products` | One entry per product slug: status (online \| degraded \| offline \| coming_soon), latency_ms (null when the check is not an HTTP probe), detail (what the check saw) and title. |


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
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
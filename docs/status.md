# API Status

Live health of every Developer-API product: online / degraded / offline, response latency and last-checked time, plus an overall rollup. Public — no API key required, never counts against your quota. Refreshed every 5 minutes. Mirrors the human status page at nakordoni.eu/{lang}/status.

**Endpoint:** `GET /api/v1/data/status`

> No API key required — public endpoint.

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/status"
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

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#status)

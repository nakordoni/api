# Multi-Checkpoint API

Fetch live queue status and/or data freshness for up to 20 checkpoints in a single request. Designed for dashboard builders who poll many PPIDs simultaneously — reduces 20+ individual calls to one. Quota counts as ⌈(N PPIDs × sub-products) / 2⌉ — 50% cheaper than the equivalent individual calls. Use include=queue,update-info to get both datasets at once.

**Endpoint:** `GET /api/v1/data/multi`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppids` | Comma-separated list of checkpoint IDs (required, max 20) — e.g. id_2,id_13,id_15,id_59 |
| `include` | Comma-separated sub-products to include: queue, update-info (default: queue,update-info) |
| `lang` | Language code for checkpoint names in queue responses (default: en) |


## Example

```bash
curl "/api/v1/data/multi?ppids=id_2,id_13,id_15&include=queue,update-info&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "multi",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#multi
*Auto-generated 2026-06-23 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
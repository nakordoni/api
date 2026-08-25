# Multi-Checkpoint API

Fetch live queue status and/or data freshness for up to 20 checkpoints in a single request. Designed for dashboard builders who poll many PPIDs simultaneously — reduces 20+ individual calls to one. Quota counts as ⌈(N PPIDs × sub-products) / 2⌉ — 50% cheaper than the equivalent individual calls. Use include=queue,update-info to get both datasets at once.

**Endpoint:** `GET /api/v1/data/multi`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppids` | Comma-separated list of checkpoint IDs (required, max 20) — e.g. id_2,id_13,id_15,id_59 |
| `include` | Comma-separated sub-products to include: queue, update-info (default: queue,update-info) |
| `lang` | Language code for checkpoint names in queue responses (default: en) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/multi?ppids=id_2,id_13,id_15&include=queue,update-info&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.<ppid>` | One object per requested checkpoint, keyed by ppid — not an array. A ppid you asked for that we do not monitor is absent. |
| `data.<ppid>.queue` | Present when include contains queue: found, queue_now, wait_min, wait_status, trend_percent, trend_direction, age_min, freshness, name, updated_at. |
| `data.<ppid>.update_info` | Present when include contains update-info: found, timestamp, datetime, timezone, age_seconds, age_minutes, freshness, queue_now. |
| `meta.ppids_requested` | How many ppids the call asked for (envelope level, beside data). |
| `meta.units_consumed` | Quota units this call actually cost — ⌈(ppids × sub-products) / 2⌉. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
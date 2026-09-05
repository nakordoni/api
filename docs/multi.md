# Multi-Checkpoint API

Fetch live queue status and/or data freshness for up to 5 checkpoints in a single request. Designed for dashboard builders who poll several PPIDs together — reduces 5 individual calls to one. Quota counts as ⌈(N PPIDs × sub-products) / 2⌉ — 50% cheaper than the equivalent individual calls. Use include=queue,update-info to get both datasets at once. From 2026-08-30 a request that lists more than 5 PPIDs is answered for its first 5 only: the rest are ignored (echoed back in meta.ppid_cap.ignored) and are not billed.

**Endpoint:** `GET /api/v1/data/multi`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppids` | Comma-separated list of checkpoint IDs (required, max 5 from 2026-08-30) — e.g. id_2,id_13,id_15. Extra IDs beyond the fifth are ignored, not rejected. |
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
| `data.<ppid>.queue` | Present when include contains queue: found, queue_now, wait_min, wait_status, trend_percent, trend_direction, age_min, freshness, name, updated_at, data_quality. |
| `data.<ppid>.update_info` | Present when include contains update-info: found, timestamp, datetime, timezone, age_seconds, age_minutes, freshness, queue_now, data_quality. |
| `data.<ppid>.*.data_quality` | On both sub-products: high = counted from a real observation source at the crossing, low = a modelled estimate where no counting source exists. Same high\|low vocabulary as the checkpoints directory, Border Queue and Best Time to Cross. Both are real answers; a low value is an estimate and should be labelled as one if you show it to drivers. |
| `meta.ppids_requested` | How many ppids the call asked for (envelope level, beside data). |
| `meta.units_consumed` | Quota units this call actually cost — ⌈(ppids × sub-products) / 2⌉, counted on the checkpoints actually answered. |
| `meta.ppid_cap` | Present ONLY when the call listed more than 5 PPIDs: cap, enforced_from, enforced, ppids_asked, ppids_answered, ignored[] and a plain-language notice. The same event also sets the X-Devapi-Warning response header. |


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
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
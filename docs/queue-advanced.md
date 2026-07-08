# Advanced Wait Time API

Wait time using the full complex formula: base wait (tmin + queue x tpercar) adjusted by two live multipliers — section_mode (current traffic-flow condition: stuck 1.4x / normal 1.0x / fast 0.75x) and weather (Tomorrow.io-backed, 0.3x-1.0x for rain/snow/wind/fog/cold). Returns the full breakdown (base_wait_min, each modifier, and the final advanced_wait_min) so consumers can see exactly how the estimate was built, alongside the simpler wait_min from the queue/multi/border products.

**Endpoint:** `GET /api/v1/data/queue-advanced`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |


## Example

```bash
curl "/api/v1/data/queue-advanced?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue-advanced",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#queue-advanced
*Auto-generated 2026-07-08 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
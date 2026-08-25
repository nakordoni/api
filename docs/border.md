# Border Queue API

All checkpoints on a given border + vehicle type in one call — live queue, wait estimate, and data freshness for every crossing point. Supports single destination, comma-separated list, or "all" to query all neighbours at once. Results sorted by queue length ascending, each checkpoint tagged with its border country.

**Endpoint:** `GET /api/v1/data/border`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `origin` | Origin country code (URL path segment): 1=Ukraine, 2=Poland, 3=Slovakia, 4=Hungary, 5=Romania, 6=Moldova, 7=Belarus, 8=Lithuania, 9=Latvia, 11=Slovenia, 12=Bulgaria, 13=Serbia, 14=Turkey, 15=North Macedonia, 16=Croatia, 17=Bosnia, 18=Germany, 19=Greece, 20=Italy, 21=Albania, 22=Montenegro, 23=Kosovo |
| `destination` | Destination (URL path segment): single country code, comma-separated list (e.g. 2,3,5), or "all" to expand to all neighbours with monitored data |
| `crossing_type` | Vehicle type (URL path segment): 4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck |
| `lang` | Language for checkpoint names in the response (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/border/1/all/4" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.origin` | Numeric country code the path asked for, plus origin_name in the requested language. |
| `data.destinations` | Numeric country codes the call expanded to, plus destination_names. |
| `data.crossing_type` | Vehicle type of every row, plus crossing_type_label. |
| `data.checkpoints[].ppid` | Checkpoint ID. |
| `data.checkpoints[].name` | Checkpoint name in the requested language. |
| `data.checkpoints[].border` | Numeric country code of the neighbour this crossing leads to, plus border_name. |
| `data.checkpoints[].queue` | Vehicles counted in the queue right now. |
| `data.checkpoints[].wait_min` | Estimated wait in minutes; null where the crossing has no wait signal. |
| `data.checkpoints[].wait_status` | Plain-language band for the wait (e.g. free_flow, moderate, heavy). |
| `data.checkpoints[].trend_percent` | Change against the recent baseline, with trend_direction (up \| down \| flat). |
| `data.checkpoints[].updated_at` | When this reading was taken, plus age_min — how old it is now. |
| `data.checkpoints[].source_url` | Public page for the checkpoint, for the attribution link. |
| `data.count` | Number of checkpoints returned; generated_at is when the set was assembled. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "border",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#border
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
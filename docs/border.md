# Border Queue API

All checkpoints on a given border + vehicle type in one call — live queue, wait estimate, and data freshness for every crossing point. Supports a single destination, a comma-separated list, or "all" (list and "all" both deprecated) to query several neighbours at once. Results sorted by queue length ascending, each checkpoint tagged with its border country.

**Endpoint:** `GET /api/v1/data/border`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `origin` | Origin country code (URL path segment): 1=Ukraine, 2=Poland, 3=Slovakia, 4=Hungary, 5=Romania, 6=Moldova, 7=Belarus, 8=Lithuania, 9=Latvia, 11=Slovenia, 12=Bulgaria, 13=Serbia, 14=Turkey, 15=North Macedonia, 16=Croatia, 17=Bosnia and Herzegovina, 18=Germany, 19=Greece, 20=Italy, 21=Albania, 22=Montenegro, 23=Kosovo. |
| `destination` | Destination (URL path segment): a single country code, a comma-separated list (e.g. 2,3,5), or "all" to expand to all neighbours with monitored data. The list and "all" are BOTH deprecated and stop working on 2026-10-06 — data is licensed per country, so a request names one country. /api/v4/ takes exactly one destination per call: send one call per destination country. |
| `crossing_type` | Vehicle type (URL path segment): 4=Car, 5=Car. Tax Free, 6=Bus, 7=Pedestrian, 8=Freight Transport, 9=Freight Transport up to 7.5 tons. Other ids exist in the checkpoint directory (ferry 10-13, freight up to 3.5 t 14, rail 15); this product answers 400 for them. |
| `lang` | Language for checkpoint names in the response (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/border/1/2/4" \
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
*Auto-generated 2026-09-07 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
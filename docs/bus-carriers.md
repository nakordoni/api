# Bus Carrier Border Stats API

Border-crossing performance per bus carrier: crossings, average/median/min/max wait minutes — built from our own plate-matched crossing records.

**Endpoint:** `GET /api/v1/data/bus-carriers`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 — or "all" for the aggregated roster |
| `days` | Period 1-90 (default 30) |
| `min_crossings` | Minimum crossings to include a carrier (default 3) |
| `limit` | Max carriers returned (default 20) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/bus-carriers?ppid=all&days=30" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.companies[].name` | Carrier, as matched from plates seen at the border. |
| `data.companies[].crossings` | How many crossings of theirs we matched in the period. |
| `data.companies[].avg_wait_min` | Their average border wait, with median_wait_min, min_wait_min and max_wait_min. |
| `data.companies[].max_wait_days_ago` | How long ago the worst wait happened — an old outlier is not today's carrier. |
| `data.total_crossings` | Crossings observed in period_days, with matched_crossings — the subset a carrier could be identified for. |
| `data.data_available` | false = too little matched data for the checkpoint/period to report anything. |
| `data.ppid` | Checkpoint the stats are for ('all' when aggregated), with mode and generated_at. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "bus-carriers",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#bus-carriers
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
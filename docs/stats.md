# Checkpoint Hourly Statistics API

Hourly historical queue stats per checkpoint and date: 24 hourly values, daily avg/min/max, peak and quietest hours, day-over-day comparison.

**Endpoint:** `GET /api/v1/data/stats`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `date` | YYYY-MM-DD (default: yesterday) |
| `compare` | 1 = include previous day + delta |
| `lang` | Language code (default uk) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/stats?ppid=id_15&date=2026-06-01&compare=1" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.ppid` | Checkpoint the statistics belong to, with checkpoint.name and checkpoint.source_url. |
| `data.date` | The day covered, in the checkpoint's timezone (data.timezone); unit says what the numbers count. |
| `data.hourly[]` | 24 elements: hour, value, data_points (observations behind that hour). value is null for an hour with no data. |
| `data.labels` | Hour labels for the 24 buckets; values is the bare value series, aligned with them. |
| `data.daily` | Whole-day rollup: avg, min, max, total, hours_with_data. |
| `data.peak` | Busiest hour of the day, and quietest the calmest — both null when the day has too little data. |
| `data.data_available` | false = we hold nothing for that date; every number above is then empty rather than zero. |
| `data.compare` | Present only with compare=1: the same shape for the previous day plus delta_avg. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "stats",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#stats
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
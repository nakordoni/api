# Best Time to Cross API

Typical-week load statistics per checkpoint: 7×24 day-of-week × hour matrix (median + p25/p75 band), quietest/busiest day, best/worst 2-hour windows. Precomputed daily from ~60 days of real observations.

**Endpoint:** `GET /api/v1/data/day-stats`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 |
| `lang` | Language for weekday/unit labels (default uk) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/day-stats?ppid=id_13&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.matrix` | 7×24 median queue: matrix[dayOfWeek][hour], day 0 = Monday, hours in the checkpoint's timezone (data.tz). |
| `data.lo` | Same 7×24 shape — the p25 floor of the band; hi is the p75 ceiling. |
| `data.counts` | Observations behind each cell — a cell with few counts is a weak median. |
| `data.weekday_avg` | Seven averages, one per weekday; best_day and worst_day index into them. |
| `data.best_slots[]` | Quietest 2-hour windows: dow, hour, v (the value). worst_slots is the same for the busiest. |
| `data.overall_avg` | Average across the whole matrix, with overall_max. |
| `data.samples` | Readings the matrix was built from, over period_days; data_quality grades that sample. |
| `data.generated_at` | When the matrix was last recomputed (unix). It is precomputed daily, not per request. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "day-stats",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#day-stats
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
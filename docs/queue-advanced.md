# Advanced Wait Time API

Wait time adjusted for live traffic flow and weather, with a full breakdown of each adjustment, plus the same wait_status/trend fields as border and multi. Billed at 1.5x a normal heavy-class call, reflecting the extra traffic/weather/driver-report lookups. Granted on request.

**Endpoint:** `GET /api/v1/data/queue-advanced`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/queue-advanced?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.queue_now` | Vehicles in the queue right now, with base_wait_min — the wait before any adjustment. |
| `data.advanced_wait_min` | Wait after every adjustment below is applied. dynamic_wait_min blends it with fresh driver reports. |
| `data.wait_status` | Band for the wait, with trend_percent and trend_direction. |
| `data.section_mode` | Measured section transit time feeding the model: value, modifier, age_min, stale. |
| `data.weather` | Weather adjustment: multiplier, reason, data_age_minutes, plus the road condition on our own 0-5 hazard scale — condition_code (0 good, 1 fog, 2 snow, 3 rain, 4 ice, 5 wind), condition (the machine key) and severity (none\|low\|moderate\|major). Raw provider readings (temperature, wind speed, the provider's own condition name) are not served: the licence covers our derived value, not the observation behind it. condition_code is null when we hold no weather for the checkpoint — 0 would claim a clear road we have not observed. |
| `data.service_rate` | Booth throughput: cars_per_min, modifier, age_min, stale. |
| `data.shift_change` | Border-shift effect: adjustment_min, shift_hour, avg_coefficient, sample_count, in_window. |
| `data.dynamic` | How driver reports were blended in: source, reports_n, reports_age_min, reported_median_min, blend_weight. |
| `data.driver_reported` | The freshest driver-reported wait itself (wait_min, ts, age_min), or null when there is none. |
| `data.exceeds_crossing_time` | true = the modelled wait came out longer than the measured total crossing time it is a part of — treat the number as unreliable. |


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
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Live Border Queue API

Real-time queue length, wait estimate and status for any monitored checkpoint, plus hourly/daily aggregates.

**Endpoint:** `GET /api/v1/data/queue`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `section` | Data section (optional) |
| `origin` | Origin country code (optional) |
| `destination` | Destination country code (optional) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/queue?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data[]` | Hourly series for the checkpoint, oldest first — one element per reading. |
| `data[].time` | Unix timestamp of the reading, in the checkpoint's own timezone. |
| `data[].avg_cars` | Vehicles in the queue at that hour. |
| `data[].wait_time` | Wait in minutes at that hour. |
| `data[].wait_time_estimated` | true = the wait is our estimate from the queue length, not an official figure. |
| `data[].type_of_data` | Provenance marker of the reading (official feed, camera count, driver report…). |
| `snapshot` | Envelope-level, beside data: the current state — queue_now, wait_min, updated_at, age_min. Read this for 'now'; data is the history behind it. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#queue
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
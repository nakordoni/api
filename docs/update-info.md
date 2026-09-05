# Live Queue & Freshness API

Current queue length for one checkpoint plus how fresh that reading is — returns queue_now, freshness, age_minutes, is_realtime, status, timestamp and timezone. This is the STANDARD-class way to poll live queue data: use it for frequent refreshes and keep the heavy quota for /queue, /multi and /forecast, which return the fuller payload (wait_min, trend, history).

**Endpoint:** `GET /api/v1/data/update-info`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `lang` | Language code |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/update-info?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.queue_now` | Vehicles in the queue at the latest reading. |
| `data.timestamp` | When that reading was taken (unix), with datetime and timezone — the checkpoint's own zone. |
| `data.age_seconds` | How old the reading is now, also as age_minutes and age_hours. |
| `data.freshness` | Plain-language band for that age. is_realtime says the row is an observation rather than a forecast point — it does NOT say the observation was counted; read data_quality for that. |
| `data.data_quality` | high = the reading was counted from a real observation source at the crossing; low = a modelled estimate, because that checkpoint has no counting source. Same high\|low vocabulary as the checkpoints directory, Border Queue and Best Time to Cross. Both are real answers; low is not an error, but do not present a low value to drivers as a measurement. |
| `data.found` | false = we monitor the checkpoint but hold no reading; status carries the reason. |
| `data.origin` | Which upstream the reading came from. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "update-info",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#update-info
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Queue Forecast API

ML ensemble forecast of queue levels: 24-hour and 7-day (168h) horizons with confidence bounds. The same model that powers nakordoni.eu predictions.

**Endpoint:** `GET /api/v1/data/forecast`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `prediction_steps` | 24 (default) or 168 for 7-day |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/forecast?ppid=id_13&prediction_steps=24" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data[]` | One element per forecast step, nearest first — 24 or 168 of them. |
| `data[].time` | Unix timestamp the step forecasts, with hours_from_now. |
| `data[].avg_cars` | Predicted queue at that hour. |
| `data[].lower_bound` | Confidence band around avg_cars, with upper_bound; the _50 pair is the narrower 50% band. |
| `data[].type_of_data` | Provenance marker of the input reading the step was built from. |
| `data[].source` | Which model produced the step, with formula and the corrected flag when a calibrator adjusted it. |
| `data[].learned_mult` | Per-checkpoint learned multiplier applied, and weather_mult the weather one. |
| `data[].v4` | Full breakdown from the v4 ensemble — component scores behind avg_cars. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "forecast",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#forecast
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
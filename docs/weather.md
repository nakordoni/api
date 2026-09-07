# Border Weather Condition API

The road-hazard condition at a border checkpoint on a single 0..5 scale: 0 clear road, 1 fog, 2 snow, 3 rain, 4 ice, 5 strong wind, named in the requested language and graded by severity. This is our own classification, not a meteorological feed — no temperature, pressure or wind readings are served, and none are needed to decide whether the approach is safe. Two writers feed the same scale: what drivers themselves report from the queue, and our weather models mapped onto it; ?sources= keeps only one of them. Every answer carries observed_at, data_age_minutes and is_fresh, and says no_data outright rather than implying a clear road when nothing was reported inside the window.

**Endpoint:** `GET /api/v1/data/weather`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `lang` | Language for condition and severity names (any of the 25 site languages; default en) |
| `sources` | all (default) \| community — only what drivers reported \| model — only our weather-model classification |
| `max_age_minutes` | How far back a marker may be and still count (default 180, min 15, max 1440). Markers are written roughly hourly per checkpoint; driver reports arrive irregularly |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/weather?ppid=id_13&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.condition.code` | The marker, 0..5, with key the stable machine name (good, fog, snow, rain, ice, wind) and name the same thing in the requested language, worded for a driver ("Slippery!", not "freezing precipitation"). null when nothing was reported inside the window — see data.no_data. |
| `data.condition.severity` | none \| low \| moderate \| major, the same severity vocabulary road-conditions uses, with severity_name in the requested language. |
| `data.condition.severity_rank` | Danger order when you sort several checkpoints: 1 ice, 2 fog, 3 snow, 4 rain, 5 wind, 6 clear. Lower is worse — the codes themselves are NOT an order. |
| `data.condition.hazard` | true for anything but a clear road, so a client can gate an alert on one field. |
| `data.observed_at` | When the marker was written (Europe/Kyiv), with observed_ts as a Unix timestamp. |
| `data.data_age_minutes` | Age of that marker in minutes, with is_fresh true when it is under 3 hours old. Read it before treating the answer as current. |
| `data.source_type` | community when a driver reported it, model when our weather model classified it. null when there is no marker. |
| `data.no_data` | true when no marker exists for this checkpoint inside the window — an honest gap, not a clear road. Widen max_age_minutes or drop the sources filter. |
| `data.scale` | The whole 0..5 vocabulary as an array indexed by code, so you can label a value without hardcoding our wording. |
| `data.sources` | The filters actually applied, with window_minutes — both echoed back after clamping. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "weather",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#weather
*Auto-generated 2026-09-07 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
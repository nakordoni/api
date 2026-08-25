# Checkpoint Alternatives API

Nearby alternative checkpoints on the same border with current queues and distance deltas. By default filters to the same vehicle type as the requested ppid. Use crossing_type to override — e.g. crossing_type=4 for cars, crossing_type=7 for pedestrians.

**Endpoint:** `GET /api/v1/data/alternatives`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID (e.g. id_13) |
| `lang` | Language code (default uk) |
| `crossing_type` | Optional vehicle type override: 4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck. Defaults to same type as the requested ppid. |
| `limit` | Max results (1–10, default 5) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/alternatives?ppid=id_13&crossing_type=4" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.ppid` | The checkpoint you asked about, with checkpoint.name, checkpoint.queue, checkpoint.unit and checkpoint.source_url. |
| `data.crossing_type_filter` | Vehicle type the alternatives were filtered to. |
| `data.alternatives[].ppid` | A nearby crossing on the same border, with name and source_url. |
| `data.alternatives[].distance_km` | Road distance from the checkpoint you asked about. |
| `data.alternatives[].queue` | Its queue now, in unit, and diff — the difference against your checkpoint (negative = shorter). |
| `data.alternatives[].is_better` | true when this crossing is currently the better bet. |
| `data.best` | The single best alternative, same fields — null when none beats your checkpoint. |
| `data.generated_at` | When the comparison was made. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "alternatives",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#alternatives
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
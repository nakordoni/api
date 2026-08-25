# Border Travel Matrix API

Travel time + border queue data for all checkpoints from a given origin. Returns drive time, current queue, total estimated journey time, and distance for every relevant crossing — sorted from fastest. Supports multi-leg routes (e.g. Germany → Poland → Ukraine border). Powers the nakordoni.eu navigator "Choose a crossing" feature.

**Endpoint:** `GET /api/v1/data/travel-matrix`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `origin_lat` | Origin latitude (required) |
| `origin_lon` | Origin longitude (required) |
| `type` | Crossing type: 4=car UA→EU (default), 5=car EU→UA, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck |
| `dest` | Destination country code (1=UA, 2=PL, 3=SK, 4=HU, 5=RO, 6=MD) or "all" (default) |
| `origin_country` | ISO-2 origin country (e.g. DE, PL, CZ). Enables multi-leg route calculation for non-border countries |
| `lang` | Language code for checkpoint names (default uk) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/travel-matrix?origin_lat=50.06&origin_lon=19.94&type=4&dest=all&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.results[].ppid` | Crossing, with checkpoint_name, country, country_id, lat and lon. |
| `data.results[].distance_km` | Road distance from your origin, with drive_minutes. |
| `data.results[].queue_minutes` | Current border wait, with queue_cars behind it. |
| `data.results[].total_minutes` | drive_minutes + queue_minutes — the ranking figure. |
| `data.results[].avg_crossing_minutes` | Typical time inside the crossing itself, with total_with_avg_crossing for a door-to-door estimate. |
| `data.results[].status` | Checkpoint status — a closed crossing still appears, so filter on it. |
| `data.origin` | The origin the matrix was computed from; count is the number of crossings, updated_at when the queues were read. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "travel-matrix",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#travel-matrix
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
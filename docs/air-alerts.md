# Air Alert Proximity API

Air-raid alerts and airborne objects near ONE border crossing. Anchor on a checkpoint (ppid), a coordinate pair or a city and get back only what is inside your radius (default 100 km): which regions are under alert, how far each is, and how close any tracked object currently is on a 1-5 proximity band. Regions are returned as ISO 3166-2 region_code, the same classifier the regional fuel data uses. DELIBERATELY NOT REAL-TIME: every response is a snapshot 1-3 minutes old with the lag re-randomised per request and stated in as_of/lag_min, and object coordinates are never returned - only distance, bearing and speed relative to your anchor. Source data is crowd/OSINT-derived and partly hand-moderated: a signal, never ground truth, and never a substitute for official air-raid warnings.

**Endpoint:** `GET /api/v2/data/air-alerts`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID to anchor on, e.g. id_94 — use the Checkpoints Directory API to discover it. Alternative to lat/lon. |
| `lat` | Latitude of the anchor point (alternative to ppid) |
| `lon` | Longitude of the anchor point (alternative to ppid) |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius_km` | Search radius in km around the anchor (default 100, min 10, max 300). The 1-5 proximity band is scaled to this radius: 5 = nearest fifth, 1 = outermost fifth. |
| `lang` | Language code (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/air-alerts?ppid=id_94&radius_km=100" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.anchor` | What the radius was measured from: ppid, lat, lng, radius_km. |
| `data.as_of` | Snapshot time — DELIBERATELY not real-time. lag_min states the delay, re-randomised per request, and lag_note explains it. |
| `data.alerts.count` | Regions under alert inside the radius, with region_codes (ISO 3166-2) and items — each with its distance from the anchor. |
| `data.alerts.unresolved` | Alerts that could not be placed on a region, with merged — how many overlapping ones were combined. |
| `data.targets.count` | Tracked objects inside the radius, with items — distance, bearing, speed and a 1-5 proximity band ONLY. Coordinates are never returned. |
| `data.targets.by_type` | Counts per object type, with tracked_total across the whole feed. |
| `data.attribution` | Source labels you must display, with a note per source. |
| `data.labels` | Localised UI strings, including the disclaimer — crowd/OSINT data, never a substitute for official air-raid warnings. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "air-alerts",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#air-alerts
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
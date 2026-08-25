# Road Conditions API

Live road conditions on the approaches to a border crossing: accidents, roadworks, closures, congestion, weather hazards, potholes and ice. Merges our own driver reports with the incident feeds we run the navigator on — national road authorities (GDDKiA, autobahn.de, ÖAMTC, Zjazdnost, Digitraffic, CCISS, DGT) plus TomTom, HERE and crowd reports — deduplicated across sources, each row carrying the `source` it came from. Without coordinates the answer is scoped to the BORDER CORRIDORS rather than a whole country: the last 50 km of the main road from each capital to the crossing, on both sides of the border. Pass ?ppid= for one crossing, or lat/lng+radius for a plain radius search anywhere.

**Endpoint:** `GET /api/v1/data/road-conditions`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code (optional). Returns that country's side of the border corridors — not a country-wide dump |
| `ppid` | Checkpoint ID, e.g. id_13 (optional). Scopes the answer to that crossing's corridor: the last 50 km of the main road to it on BOTH sides of the border |
| `condition_type` | accident\|roadwork\|closure\|congestion\|weather\|hazard\|pothole\|speed_bump\|object\|stopped\|ice\|other (optional). In v1 anything an upstream feed reports outside this list is mapped to `other`; v2 returns the raw value |
| `severity` | low\|moderate\|major\|critical (optional) |
| `lat` | Latitude (optional) — with lng, switches to a plain radius search |
| `lng` | Longitude (optional) |
| `radius` | Radius km (default 50). Applies ONLY with lat+lng; on the corridor path the road itself is the filter, not a circle |
| `sources` | all (default) \| user (our own driver reports only) \| external (road-authority and provider feeds only) |
| `limit` | Max results (default 100, cap 500) |
| `offset` | Pagination offset (optional) |
| `include_expired` | 1 = also return conditions whose expiry has passed (optional) |
| `lang` | Language for condition_type_name / severity_name labels — all 24 site languages (default uk) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/road-conditions?ppid=id_13&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.conditions[].condition_type` | What it is (roadworks, closure, accident, weather…), with condition_type_name in the requested language. |
| `data.conditions[].severity` | How bad it is, with severity_name. |
| `data.conditions[].description` | Free-text detail as published, with road_name and direction. |
| `data.conditions[].latitude` | Where it is, with longitude and country_code. |
| `data.conditions[].corridor_ppid` | The border crossing whose approach corridor this sits on, when it is on one. |
| `data.conditions[].estimated_duration` | How long it is expected to last, with expires_at and created_at. |
| `data.conditions[].source` | Upstream that reported it, with external_id — stable across refreshes, use it to deduplicate. |
| `data.total` | Matches for the query, with limit and offset for paging. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "road-conditions",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#road-conditions
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
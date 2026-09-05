# Checkpoints Directory API

Directory of all monitored border checkpoints: IDs, names, countries, coordinates and status. Use it to discover ppid values for the other APIs. Each row carries has_day_stats — false means the Best Time to Cross API has no matrix for that checkpoint and would answer 404, so skip it instead of polling. Three more flags say what is available RIGHT NOW: has_live_data (is there a live queue value at all), last_observation (how old it is) and data_quality (high = counted at the crossing, low = a modelled estimate) — so you can tell an observed queue from an estimate, and a live one from one that stopped moving, before you poll.

**Endpoint:** `GET /api/v1/data/checkpoints`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | Filter by country: either the ISO alpha-2 code (TR, GR, PL …) or the numeric checkpoint country code (1=UA, 2=PL, 3=SK, 4=HU, 5=RO, 6=MD, 7=BY, 8=LT, 9=LV, 14=TR, 19=GR, …). An unknown value answers 400 invalid_country listing every accepted value — it no longer returns an empty list. |
| `lang` | Language for checkpoint names (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/checkpoints" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.checkpoints[].ppid` | Checkpoint ID — the value every other product's ppid parameter takes. |
| `data.checkpoints[].name` | Checkpoint name in the requested language. |
| `data.checkpoints[].origin` | Numeric country code of the side the crossing is operated from. |
| `data.checkpoints[].destination` | Numeric country code of the country across the border. |
| `data.checkpoints[].crossing_type` | Vehicle type this row is monitored for: 4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck. |
| `data.checkpoints[].timezone` | IANA timezone of the crossing — every timestamp for it is in this zone. |
| `data.checkpoints[].status` | 1=open, 3=closed, 0=inactive. |
| `data.checkpoints[].redirect_to` | Present only on a checkpoint that was RETIRED as a duplicate of another one at the same physical crossing: the ppid that superseded it. Such a row always carries status 0 — point your integration at the id named here. The retired id still answers the other products for now, but it is not the canonical one and is not guaranteed to keep updating. |
| `data.checkpoints[].has_day_stats` | false means the Best Time to Cross product has no matrix for this checkpoint and would answer 404 — skip it rather than poll it. |
| `data.checkpoints[].has_live_data` | Whether a live queue value currently exists for this checkpoint, i.e. whether the queue and border products will return a number for it rather than nothing. It says nothing about how recent that number is — read last_observation for that. About 65 of the monitored checkpoints have no live source at all. Absent from every row if the live-value store could not be read at all, so treat "key missing" as unknown, not as false. |
| `data.checkpoints[].last_observation` | ISO 8601 timestamp of the live value behind has_live_data, or null when there is none. Some checkpoints keep answering with a value that stopped moving long ago — a few of them years ago — so check this age before presenting the queue as current. |
| `data.checkpoints[].data_quality` | How the current live value was arrived at: high = counted from a real observation source at the crossing, low = a modelled estimate where no counting source exists. Same vocabulary as the Best Time to Cross and live queue products. Present only when has_live_data is true; omitted otherwise. Both are real answers — low is not an error — but a low value is an estimate and should be labelled as one if you show it to drivers. |
| `data.count` | Number of checkpoints returned. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "checkpoints",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#checkpoints
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
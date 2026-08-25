# Checkpoints Directory API

Directory of all monitored border checkpoints: IDs, names, countries, coordinates and status. Use it to discover ppid values for the other APIs. Each row carries has_day_stats — false means the Best Time to Cross API has no matrix for that checkpoint and would answer 404, so skip it instead of polling.

**Endpoint:** `GET /api/v1/data/checkpoints`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | Filter by numeric country code (1=UA, 2=PL, 3=SK, 4=HU, 5=RO, 6=MD, 7=BY, 8=LT, 9=LV, …) |
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
| `data.checkpoints[].has_day_stats` | false means the Best Time to Cross product has no matrix for this checkpoint and would answer 404 — skip it rather than poll it. |
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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
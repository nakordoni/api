# Travel Matrix API

Travel time + border queue data for all checkpoints from a given origin. Returns drive time, current queue, total estimated journey time, and distance for every relevant crossing — sorted from fastest. Supports multi-leg routes (e.g. Germany → Poland → Ukraine border). Powers the nakordoni.eu navigator "Choose a crossing" feature.

**Endpoint:** `GET /api/v1/data/travel-matrix`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

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
curl "/api/v1/data/travel-matrix?origin_lat=50.06&origin_lon=19.94&type=4&dest=all&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

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
*Auto-generated 2026-06-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
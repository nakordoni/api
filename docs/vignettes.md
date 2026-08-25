# Vignettes & Road Tolls API

Whether a country requires a vignette for highway travel, current prices per duration and where to read more — per country or for a whole trip.

**Endpoint:** `GET /api/v2/data/vignettes`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code (e.g. AT, CH, SI) |
| `lang` | Language code (default en) |


## Example

```bash
curl "/api/v2/data/vignettes?country=AT" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "vignettes",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#vignettes
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
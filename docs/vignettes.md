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
curl "https://nakordoni.eu/api/v2/data/vignettes?country=AT" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.country` | Country name, with country_code (ISO-2). |
| `data.required` | false = no vignette is needed to drive there; info and prices then explain what applies instead (tolls, none at all). |
| `data.info` | What the vignette covers and who needs it, in the requested language. |
| `data.prices` | Current price tiers as published by the operator. |
| `data.note` | Extra caveat when one applies, otherwise null. |
| `data.more_info_url` | Official source page — link it when you display the prices. |


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
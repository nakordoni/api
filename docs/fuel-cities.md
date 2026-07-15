# Fuel Prices by City API

Per-city fuel price summary for a country: cheapest station price and average across the top 5 stations in each major city. Covers the same countries as the nakordoni.eu fuel pages (AT, DE, FR, ES, IT, PT, SI, LU, RO, DK, HR).

**Endpoint:** `GET /api/v1/data/fuel-cities`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code (required). Supported: at, de, fr, es, it, pt, si, lu, ro, dk, hr |
| `fuel_type` | diesel (default) | e5 | e10 | lpg — see available_fuel_types in the response for what each country supports |
| `lang` | Language code for city names (default en) |


## Example

```bash
curl "/api/v1/data/fuel-cities?country=hr&fuel_type=diesel&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fuel-cities",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fuel-cities
*Auto-generated 2026-07-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Currency Exchange Rates API

EUR-based exchange rates for PLN, CZK, HUF, USD, GBP, CHF, NOK and UAH — sourced from Frankfurter (ECB), cached 6h. No parameters; always returns the full rate table. Powers the nakordoni.eu currency calculator and fuel-cost pages.

**Endpoint:** `GET /api/v1/data/currency`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "/api/v1/data/currency" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "currency",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#currency
*Auto-generated 2026-07-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
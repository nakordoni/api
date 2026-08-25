# Currency Exchange Rates API

EUR-based exchange rates for PLN, CZK, HUF, USD, GBP, CHF, NOK and UAH — sourced from Frankfurter (ECB), cached 6h. No parameters; always returns the full rate table. Powers the nakordoni.eu currency calculator and fuel-cost pages.

**Endpoint:** `GET /api/v1/data/currency`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "https://nakordoni.eu/api/v1/data/currency" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.rates` | Rate per currency against data.base (EUR = 1): CHF, CZK, GBP, HUF, NOK, PLN, RON, USD, UAH. |
| `data.base` | The currency every rate is quoted against. |
| `data.date` | Date of the quoted rates, with source (ECB via Frankfurter). Cached 6 h — not intraday. |
| `data.success` | false = the upstream rate table could not be refreshed and nothing is returned. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
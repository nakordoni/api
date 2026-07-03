# Trading Sundays API

Sunday retail-opening regulations and upcoming trading Sundays per regulated EU country.

**Endpoint:** `GET /api/v1/data/trading-sundays`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO country code (optional) |


## Example

```bash
curl "/api/v1/data/trading-sundays?country=PL" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "trading-sundays",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#trading-sundays
*Auto-generated 2026-07-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
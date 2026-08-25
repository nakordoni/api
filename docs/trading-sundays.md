# Trading Sundays API

Sunday retail-opening regulations and upcoming trading Sundays per regulated EU country.

**Endpoint:** `GET /api/v1/data/trading-sundays`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO country code (optional) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/trading-sundays?country=PL" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.countries[].country` | ISO-2 country, with regulated — whether Sunday trading is restricted there at all. |
| `data.countries[].regulation_type` | How it is regulated (nationwide ban with exceptions, regional rules, none). |
| `data.countries[].count` | Trading Sundays in data.year for that country. |
| `data.countries[].next` | The next trading Sunday from today, null when the year holds no more. |
| `data.mode` | What the answer covers — one country or the whole set; regulated_only echoes the filter. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
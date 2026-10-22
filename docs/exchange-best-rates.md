# Best Exchange Rates API

Where to buy or sell one currency for PLN in one city right now: the offices with the best RETAIL rates, ranked, next to the official NBP reference rate. Only rates no older than 6 hours compete, and quotes far off the city median are left out as likely errors. Wholesale rates never enter the ranking.

**Endpoint:** `GET /api/v2/data/exchange-best-rates`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `city` | City id from exchange-cities, e.g. warszawa (required) |
| `currency` | Currency to compare against PLN, ISO 4217 code such as EUR (required) |
| `limit` | Offices per list, 1-20 (default 5) |
| `lang` | Language for city name and page links (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/exchange-best-rates?city=warszawa&currency=EUR&limit=5" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.best_to_buy_currency[]` | Offices with the LOWEST sell rate — where you buy the currency cheapest. rate is that sell rate in PLN per 1 unit. |
| `data.best_to_sell_currency[]` | Offices with the HIGHEST buy rate — where you get the most PLN for the currency. |
| `data.best_to_buy_currency[].vs_nbp_pct` | Difference between rate and the NBP mid rate, in percent. |
| `data.best_to_buy_currency[].rate_time` | When the rate was valid, ISO 8601 with the Polish UTC offset; also office_id, name, address, lat, lon, buy, sell, spread_pct and url. |
| `data.candidates` | Offices with a recent enough rate; fewer than 2 means both lists are empty — there is nothing to compare. |
| `data.nbp` | National Bank of Poland reference rate: mid (table A), bid/ask (table C) where published, table and effective_date; null for a currency NBP does not publish. |
| `data.max_rate_age_minutes` | Oldest rate allowed into the ranking (360). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "exchange-best-rates",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#exchange-best-rates
*Auto-generated 2026-10-22 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
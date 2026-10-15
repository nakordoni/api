# Exchange Rate History API

Retail rate history for one currency against PLN, either for one office or for a whole city (average and best rate across its offices), hourly or as daily averages, with the NBP reference rate for the same days. History is kept for the last 92 days; office series start on 2026-10-14, city series on 2026-08-01.

**Endpoint:** `GET /api/v2/data/exchange-history`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `id` | Office id from exchange-offices — office history (give id or city) |
| `city` | City id from exchange-cities — city history (give id or city) |
| `currency` | ISO 4217 code such as EUR (required) |
| `days` | Days back from now, 1-92 (default 7) |
| `interval` | hour (default) or day — day averages each Polish calendar day |
| `lang` | Language code (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/exchange-history?city=krakow&currency=EUR&days=30&interval=day" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.points[].time` | Start of the hour (or day) the point covers, ISO 8601 with the Polish UTC offset. |
| `data.points[].buy` | Office history: the office's buy and sell rate in PLN per 1 unit. |
| `data.points[].avg_buy` | City history: average buy and sell across offices (avg_buy, avg_sell), best buy and sell (best_buy, best_sell) and how many offices were counted (offices). |
| `data.nbp[]` | NBP mid rate per business day in the window (date, mid). |
| `data.history_since` | Oldest point held for this series; retention_days is how far back the API serves. |
| `data.scope` | office or city, with id and city. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "exchange-history",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#exchange-history
*Auto-generated 2026-10-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
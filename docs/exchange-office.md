# Exchange Office Rates API

One exchange office with its current buy and sell rates against PLN. Retail and wholesale rates are returned separately: wholesale applies only from min_amount_pln and must not be compared with retail. Every rate carries its own time, so an old quote is never presented as today's.

**Endpoint:** `GET /api/v2/data/exchange-office`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `id` | Office id from exchange-offices (required) |
| `lang` | Language for city name and page link (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/exchange-office?id=3" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.office` | Every field of an exchange-offices row, plus rates. |
| `data.office.rates.retail[].currency` | ISO 4217 code; buy and sell are PLN per 1 unit (buy = what the office pays you, sell = what you pay the office). |
| `data.office.rates.retail[].rate_time` | When this rate was valid, ISO 8601 with the Polish UTC offset (e.g. 2026-10-14T15:47:00+02:00). |
| `data.office.rates.retail[].is_current` | false = the rate is older than 72 hours; show it only together with rate_time. |
| `data.office.rates.wholesale[].min_amount_pln` | Smallest transaction (in PLN) the wholesale rate applies to; null when the office does not state one. Other fields as in retail. |
| `data.office.rates.quote_currency` | Currency every rate is quoted in (PLN). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "exchange-office",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#exchange-office
*Auto-generated 2026-10-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
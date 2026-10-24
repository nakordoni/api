# Exchange Office Cities API

Polish cities where we list currency exchange offices (kantors), with how many offices each has and how many currently publish rates. Use the city id with the other exchange products.

**Endpoint:** `GET /api/v2/data/exchange-cities`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lang` | Language for city names (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/exchange-cities?lang=en" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.cities[].id` | City id — the value the city parameter of the other exchange products takes. |
| `data.cities[].name` | City name in the requested language, with country (always PL), lat and lon. |
| `data.cities[].type` | border = a border town, hub = a large city. |
| `data.cities[].offices` | Listed exchange offices in the city. |
| `data.cities[].offices_with_current_rates` | Offices whose rates were updated in the last 72 hours. |
| `data.quote_currency` | Currency every rate is quoted in (PLN). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "exchange-cities",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#exchange-cities
*Auto-generated 2026-10-24 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
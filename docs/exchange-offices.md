# Exchange Offices API

Directory of currency exchange offices (kantors) in Poland: name, address, coordinates, opening hours, phone, website and whether the office also quotes wholesale rates. Filter by city, by a currency the office quotes, or to offices open right now (Polish local time).

**Endpoint:** `GET /api/v2/data/exchange-offices`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `city` | City id from exchange-cities, e.g. warszawa (optional; omit for every city) |
| `currency` | Only offices that quote this currency, ISO 4217 code such as EUR (optional) |
| `open_now` | 1 = only offices open right now, by their published hours in Polish local time (optional) |
| `limit` | Max offices returned, 1-300 (default 100) |
| `lang` | Language for city names and page links (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/exchange-offices?city=krakow&currency=EUR&open_now=1" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.offices[].id` | Office id — the value exchange-office and exchange-history take. |
| `data.offices[].name` | Office name, with address, postal_code, city and city_name. |
| `data.offices[].lat` | Coordinates (lat, lon); null when unknown. |
| `data.offices[].hours[]` | Opening hours, one row per opening: day (mo..su), open, close (HH:MM, Polish local time). A day with no row is closed. |
| `data.offices[].open_now` | Whether the office is open now by its published hours; null when no hours are known. |
| `data.offices[].phones[]` | Phone numbers as published, with website. |
| `data.offices[].has_wholesale` | true = the office also quotes wholesale rates, which apply from a minimum amount (see exchange-office). |
| `data.offices[].has_current_rates` | true = at least one rate was updated in the last 72 hours. |
| `data.offices[].currencies[]` | Currencies the office quotes at retail. |
| `data.offices[].url` | The office page on nakordoni.eu — link it where you show the data. |
| `data.count` | Offices returned; total is the number matching the filters and truncated is true when limit cut the list. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "exchange-offices",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#exchange-offices
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
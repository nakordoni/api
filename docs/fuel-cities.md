# Fuel Prices by City API

Per-city fuel price summary for a country: cheapest station price and average across the top 5 stations in each major city. City-level data today: AT, DE, ES, FR, HR, IT, LU, PT, SI, DK — RO is accepted but has no priced stations yet and returns no cities. Which grades a country can answer differs sharply (SI, IT and PT carry diesel only; HR diesel and lpg; DE, AT, FR and LU carry most of the range), so every response reports `available_fuel_types` derived from the station data actually reachable from that country's cities — read it before choosing `fuel_type`. Asking for a grade the country has no price for returns `cities: []` and a `note` naming what is available; it is never silently answered with a different grade.

**Endpoint:** `GET /api/v1/data/fuel-cities`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code (required). Accepted: at, de, fr, es, it, pt, si, lu, ro, dk, hr — ro currently answers with no cities (no priced stations recorded) |
| `fuel_type` | diesel (default) | e5 | e10 | superplus | super100 | premdiesel | truckdiesel | hvo | lpg | cng | adblue | e85 | lng — availability is per country, not per platform. Read `available_fuel_types` in the response and pick from it; a grade absent from that list returns an empty `cities` array with a `note`, not a fallback grade. Local pump names are accepted as well — ON, Olej napędowy, Pb95, Nafta, Dizel, Gázolaj, Motorină, Benzină 95, ДП, А-95, Motorin, Gasóleo, Sans plomb 95, Bleifrei … — resolved against `country` (or, for coordinate products, the country the point falls in), because the same wording is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one. The response echoes `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade — the answer comes back empty and says so. Full table of names per country: /api/v2/data/fuel-grades. |
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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
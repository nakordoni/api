# Fuel Grade Names API

The naming table behind every fuel product: our canonical grade vocabulary (diesel, premdiesel, truckdiesel, hvo, petrol92, e5, e10, superplus, super100, e85, lpg, cng, lng, adblue) and what each grade is called at a pump in 41 European countries — ON and Olej napędowy in Poland, Nafta in Czechia, Gázolaj in Hungary, Motorină in Romania and Moldova, ДП in Ukraine, Motorin in Turkey, Gasóleo in Portugal and Spain. Every name listed here is accepted by the fuel_type parameter of the fuel, fuel-local, fuel-stations, fuel-cheapest and fuel-cities products, so you can pass a driver's own wording straight through. Resolution is country-first because the same word is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one — send `country` together with a local name. This is a NAMING table and is deliberately wider than our price coverage: it answers what a grade is called in a country whether or not we quote a price there (we hold no station prices for TR or MD at all), and `station_level` on each grade says only which grades station feeds report anywhere. Pass `fuel_type` to probe one name and get back the canonical grade it resolves to, or null when we cannot place it — we never guess a default grade.

**Endpoint:** `GET /api/v2/data/fuel-grades`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | Optional ISO-2 filter — return just that country's local names instead of all 41 (PL, DE, UA, TR, MD, …). 404 when the country is not in the table. |
| `fuel_type` | Optional probe: resolve one name in the context of `country` and report the canonical grade under `resolved` (e.g. country=PL&fuel_type=ON → diesel; country=DK&fuel_type=95 → e10; country=PL&fuel_type=95 → e5). null means we could not place it. |


## Example

```bash
curl "/api/v2/data/fuel-grades?country=PL&fuel_type=ON" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fuel-grades",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fuel-grades
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
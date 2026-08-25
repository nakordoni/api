# EU Fuel Prices API

Fuel prices across EU countries — country averages, nearest stations by coordinates, or stations near a border checkpoint. Aggregated from official national price registries and market data. A single-country answer carries a `grades` object mapping each price key (petrol, petrol95plus, petrol92, petrol98, diesel, lpg) to its canonical grade and to the local pump name for that country; the all-countries summary carries the canonical mapping only, as `grade_keys`.

**Endpoint:** `GET /api/v1/data/fuel`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `mode` | country (default) | nearest | border. country = national average; nearest = stations near lat/lon; border = stations near a checkpoint |
| `country` | ISO country code for mode=country (PL, DE, AT, FR, ES, IT, PT, SI, LU, RO, DK, HR, HU, SK, UA, …) |
| `lat` | Latitude for mode=nearest |
| `lon` | Longitude for mode=nearest (alias: lng) |
| `radius_km` | Search radius km for mode=nearest (default 30; alias: radius) |
| `ppid` | Checkpoint ID for mode=border (e.g. id_13) |
| `fuel_type` | Optional, for mode=nearest: diesel | e5 | e10 | superplus | super100 | premdiesel | truckdiesel | hvo | lpg | cng | adblue | e85 | lng (availability varies by station/region). Local pump names are accepted as well — ON, Olej napędowy, Pb95, Nafta, Dizel, Gázolaj, Motorină, Benzină 95, ДП, А-95, Motorin, Gasóleo, Sans plomb 95, Bleifrei … — resolved against `country` (or, for coordinate products, the country the point falls in), because the same wording is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one. The response echoes `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade — the answer comes back empty and says so. Full table of names per country: /api/v2/data/fuel-grades. |
| `lang` | Language code for labels |
| `limit` | Max stations returned for mode=nearest/border (default 5) |


## Example

```bash
curl "/api/v1/data/fuel?country=PL" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fuel",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fuel
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
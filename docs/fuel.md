# EU Fuel Prices API

Fuel prices across EU countries — country averages, nearest stations by coordinates, or stations near a border checkpoint. Aggregated from official national price registries and market data. A single-country answer carries a `grades` object mapping each price key (petrol, petrol95plus, petrol92, petrol98, diesel, lpg) to its canonical grade and to the local pump name for that country; the all-countries summary carries the canonical mapping only, as `grade_keys`.

**Endpoint:** `GET /api/v1/data/fuel`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `mode` | country (default) \| nearest \| border. country = national average; nearest = stations near lat/lon; border = stations near a checkpoint |
| `country` | ISO country code for mode=country (PL, DE, AT, FR, ES, IT, PT, SI, LU, RO, DK, HR, HU, SK, UA, …) |
| `lat` | Latitude for mode=nearest |
| `lon` | Longitude for mode=nearest (alias: lng) |
| `radius_km` | Search radius km for mode=nearest (default 30; alias: radius) |
| `ppid` | Checkpoint ID for mode=border (e.g. id_13) |
| `fuel_type` | Optional, for mode=nearest: diesel \| e5 \| e10 \| superplus \| super100 \| premdiesel \| truckdiesel \| hvo \| lpg \| cng \| adblue \| e85 \| lng (availability varies by station/region). Local pump names are accepted as well — ON, Olej napędowy, Pb95, Nafta, Dizel, Gázolaj, Motorină, Benzină 95, ДП, А-95, Motorin, Gasóleo, Sans plomb 95, Bleifrei … — resolved against `country` (or, for coordinate products, the country the point falls in), because the same wording is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one. The response echoes `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade — the answer comes back empty and says so. Full table of names per country: /api/v2/data/fuel-grades. |
| `lang` | Language code for labels |
| `limit` | Max stations returned for mode=nearest/border (default 5) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/fuel?country=PL" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.data.country` | ISO-2 country the averages belong to, with country_name in the requested language. |
| `data.data.petrol` | National average for 95-octane petrol, in data.data.currency. petrol98 is the 98-octane average, petrol92 the 92-octane one and petrol95plus the additive-enhanced 95 — each is null where the country does not report it. |
| `data.data.diesel` | National average for diesel. |
| `data.data.lpg` | National average for LPG, in the country's own currency; lpg_eur is the same figure converted to EUR for cross-country comparison. |
| `data.data.updated` | Date the averages are from — daily or weekly depending on the source, never live. |
| `data.data.trend_petrol` | up \| down \| null against the previous reading, and trend_diesel the same for diesel. |
| `data.grades` | One entry per price key above: grade (our canonical code), local_name (what a driver in that country calls it) and premium. Additive — the price keys themselves are unchanged. |
| `data.grade_keys` | Returned instead of grades when you ask for every country at once: the price key → canonical grade mapping only, without the per-country local names. |
| `data.labels` | Localised UI strings for the keys above — for rendering, not data. |


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
*Auto-generated 2026-09-05 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
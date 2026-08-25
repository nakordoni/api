# Local Fuel Price API

Best available fuel price for ANY point in Europe, resolved down a three-tier fallback ladder so a coordinate never comes back empty where we have data at all: pump prices from the nearest stations when the point is inside station-level coverage (DE, IT, FR, AT, ES, SI, HR, LU, PT, DK, plus PL for the Tricity area and the DE/PL border crossings only), else the regional average for the administrative region the point falls in (Ukraine only today — the UA oblast feed, in UAH), else the national average for the country. Every response carries a `resolution` field naming the tier that answered — `station` (data is a list of stations with distance_km, each in its own currency), `region` (one oblast-average object plus `region`, an ISO 3166-2 code such as UA-46, `region_name` and `region_center_dist_km`) or `country` (one national-average object). The region and country tiers return the same price keys: petrol/petrol95plus/petrol92/diesel/lpg + currency + updated date. Branch on `resolution`, never on the shape. Ukraine has no per-station prices anywhere, so a UA point answers at `region` where the oblast is quoted and falls back to `country` otherwise. 404 when the point is outside every covered country (mid-ocean, outside Europe). The region and country tiers also carry a `grades` object mapping each price key to its canonical grade and to the name a driver in that country would use at the pump (UA `diesel` → ДП, PL `petrol` → Pb95). For a plain national average without coordinates use the `fuel` product, for a pure station search `fuel-stations`, for price ranking `fuel-cheapest`.

**Endpoint:** `GET /api/v2/data/fuel-local`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius_km` | Station-tier search radius km (default 25, max 25; alias: radius). No station inside it means the answer falls through to the country tier — it never widens the search on its own. |
| `fuel_type` | Optional station-tier filter: diesel | e5 | e10 | superplus | super100 | premdiesel | truckdiesel | hvo | lpg | cng | adblue | e85 | lng. Ignored at country tier, which always returns every grade the country reports. Local pump names are accepted as well — ON, Olej napędowy, Pb95, Nafta, Dizel, Gázolaj, Motorină, Benzină 95, ДП, А-95, Motorin, Gasóleo, Sans plomb 95, Bleifrei … — resolved against `country` (or, for coordinate products, the country the point falls in), because the same wording is not the same grade everywhere: “95” is E10 at a Danish, Finnish, French, British, Belgian or Dutch pump and E5 at a Polish or German one. The response echoes `fuel_type` (canonical), `fuel_type_requested` (as you typed it) and `fuel_type_local`. A name we cannot place is never swapped for a default grade — the answer comes back empty and says so. Full table of names per country: /api/v2/data/fuel-grades. |
| `limit` | Max stations at station tier (default 5, max 20) |
| `lang` | Language for labels and the country name (default en) |


## Example

```bash
curl "/api/v2/data/fuel-local?lat=50.45&lon=30.52&lang=uk" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fuel-local",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fuel-local
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
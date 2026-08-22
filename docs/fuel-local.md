# Local Fuel Price API

Best available fuel price for ANY point in Europe, resolved down a fallback ladder so a coordinate never comes back empty where we have data at all: pump prices from the nearest stations when the point is inside station-level coverage (DE, IT, FR, AT, ES, SI, HR, LU, PT, DK, plus PL for the Tricity area only), otherwise the national average for the country the point falls in (incl. Ukraine, where no per-station price data exists anywhere — UA always answers at country level, in UAH). Every response carries a `resolution` field naming the tier that answered — `station` (data is a list of stations with distance_km, each in its own currency) or `country` (data is one national-average object: petrol/petrol95plus/petrol92/diesel/lpg + currency + updated date). Branch on `resolution`, never on the shape. 404 when the point is outside every covered country (mid-ocean, outside Europe). For a plain national average without coordinates use the `fuel` product, for a pure station search `fuel-stations`, for price ranking `fuel-cheapest`.

**Endpoint:** `GET /api/v1/data/fuel-local`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `lat` | Latitude |
| `lon` | Longitude |
| `city` | City name — alternative to lat/lon, resolved to coordinates |
| `country` | ISO-2 country code, disambiguates city |
| `radius_km` | Station-tier search radius km (default 25, max 25; alias: radius). No station inside it means the answer falls through to the country tier — it never widens the search on its own. |
| `fuel_type` | Optional station-tier filter: diesel | e5 | e10 | superplus | super100 | premdiesel | truckdiesel | hvo | lpg | cng | adblue | e85 | lng. Ignored at country tier, which always returns every grade the country reports. |
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
*Auto-generated 2026-08-22 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
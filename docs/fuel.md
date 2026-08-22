# EU Fuel Prices API

Fuel prices across EU countries — country averages, nearest stations by coordinates, or stations near a border checkpoint. Aggregated from official national price registries and market data.

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
| `fuel_type` | Optional, for mode=nearest: diesel | e5 | e10 | superplus | super100 | premdiesel | truckdiesel | hvo | lpg | cng | adblue | e85 | lng (availability varies by station/region) |
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
*Auto-generated 2026-08-22 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
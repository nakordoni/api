# EU Fuel Prices API

Average petrol/diesel/LPG prices across EU countries plus nearest stations, aggregated from official national sources.

**Endpoint:** `GET /api/v1/data/fuel?country=PL`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `country` | ISO country code (optional) |
| `lat` | Latitude (optional) |
| `lon` | Longitude (optional) |
| `lang` | Language code |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/fuel?country=PL" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
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

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#fuel)

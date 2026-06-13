# Driver POIs API

Truck parkings (14k+), free showers, services and supermarkets across Europe with coordinates.

**Endpoint:** `GET /api/v1/data/pois?type=parking&lat=50.7&lon=23.9&radius=50`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `type` | parking|shower|supermarket|industrial |
| `lat` | Latitude |
| `lon` | Longitude |
| `radius` | Radius km |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/pois?type=parking&lat=50.7&lon=23.9&radius=50" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "pois",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#pois)

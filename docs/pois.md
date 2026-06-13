# Driver POIs API

Truck parkings (14k+), free showers, services and supermarkets across Europe with coordinates.

**Endpoint:** `GET /api/v1/data/pois`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `type` | parking|shower|supermarket|industrial |
| `lat` | Latitude |
| `lon` | Longitude |
| `radius` | Radius km |


## Example

```bash
curl "/api/v1/data/pois?type=parking&lat=50.7&lon=23.9&radius=50" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
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

Full docs: https://nakordoni.eu/en/developers/docs#pois
*Auto-generated 2026-06-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
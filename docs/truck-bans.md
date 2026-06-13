# Truck Driving Bans API

European truck driving restrictions by country and date, including seasonal and holiday bans.

**Endpoint:** `GET /api/v1/data/truck-bans?country=PL`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `country` | ISO country code (optional) |
| `date` | YYYY-MM-DD (optional) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/truck-bans?country=PL" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "truck-bans",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#truck-bans)

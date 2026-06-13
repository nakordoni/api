# Trading Sundays API

Sunday retail-opening regulations and upcoming trading Sundays per regulated EU country.

**Endpoint:** `GET /api/v1/data/trading-sundays?country=PL`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `country` | ISO country code (optional) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/trading-sundays?country=PL" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "trading-sundays",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#trading-sundays)

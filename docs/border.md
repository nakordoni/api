# Border Queue API

All checkpoints on a given border + vehicle type in one call — live queue, wait estimate, and data freshness for every crossing point. Supports single destination, comma-separated list, or "all" to query all neighbours at once. Results sorted by queue length ascending, each checkpoint tagged with its border country.

**Endpoint:** `GET /api/v1/data/border/1/all/4`

> Counts against **forecast+stats** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `origin` | Origin country code (URL path segment): 1=Ukraine, 2=Poland, 3=Slovakia, 4=Hungary, 5=Romania, 6=Moldova, 7=Belarus, 8=Lithuania, 9=Latvia, 10=Estonia, 11=Slovenia, 12=Bulgaria, 13=Serbia, 14=Turkey, 15=North Macedonia, 16=Croatia, 17=Bosnia, 18=Germany, 19=Greece, 20=Italy, 21=Albania, 22=Montenegro, 23=Kosovo |
| `destination` | Destination (URL path segment): single country code, comma-separated list (e.g. 2,3,5), or "all" to expand to all neighbours with monitored data |
| `crossing_type` | Vehicle type (URL path segment): 4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck |
| `lang` | Language for checkpoint names in the response (default en) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/border/1/all/4" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "border",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#border)

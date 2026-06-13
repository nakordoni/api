# Checkpoints Directory API

Directory of all monitored border checkpoints: IDs, names, countries, coordinates and status. Use it to discover ppid values for the other APIs.

**Endpoint:** `GET /api/v1/data/checkpoints`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `country` | Filter by numeric country code (1=UA, 2=PL, 3=SK, 4=HU, 5=RO, 6=MD, 7=BY, 8=LT, 9=LV, …) |
| `lang` | Language for checkpoint names (default en) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/checkpoints" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "checkpoints",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#checkpoints)

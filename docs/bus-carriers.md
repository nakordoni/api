# Bus Carrier Border Stats API

Border-crossing performance per bus carrier: crossings, average/median/min/max wait minutes — built from our own plate-matched crossing records.

**Endpoint:** `GET /api/v1/data/bus-carriers?ppid=all&days=30`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `ppid` | Checkpoint ID or "all" for aggregated |
| `days` | Period 1-90 (default 30) |
| `min_crossings` | Minimum crossings to include a carrier (default 3) |
| `limit` | Max carriers returned (default 20) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/bus-carriers?ppid=all&days=30" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "bus-carriers",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#bus-carriers)

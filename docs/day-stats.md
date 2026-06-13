# Best Time to Cross API

Typical-week load statistics per checkpoint: 7×24 day-of-week × hour matrix (median + p25/p75 band), quietest/busiest day, best/worst 2-hour windows. Precomputed daily from ~60 days of real observations.

**Endpoint:** `GET /api/v1/data/day-stats?ppid=id_13&lang=en`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `ppid` | Checkpoint ID, e.g. id_13 |
| `lang` | Language for weekday/unit labels (default uk) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/day-stats?ppid=id_13&lang=en" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "day-stats",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#day-stats)

# Checkpoint Hourly Statistics API

Hourly historical queue stats per checkpoint and date: 24 hourly values, daily avg/min/max, peak and quietest hours, day-over-day comparison.

**Endpoint:** `GET /api/v1/data/stats?ppid=id_15&date=2026-06-01&compare=1`

> Counts against **forecast+stats** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `ppid` | Checkpoint ID |
| `date` | YYYY-MM-DD (default: yesterday) |
| `compare` | 1 = include previous day + delta |
| `lang` | Language code (default uk) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/stats?ppid=id_15&date=2026-06-01&compare=1" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "stats",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#stats)

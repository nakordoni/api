# Live Border Queue API

Real-time queue length, wait estimate and status for any monitored checkpoint, plus hourly/daily aggregates.

**Endpoint:** `GET /api/v1/data/queue?ppid=id_13`

> Counts against **forecast+stats** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `ppid` | Checkpoint ID, e.g. id_13 (see /api/v1/data/checkpoints) |
| `section` | Data section (optional) |
| `origin` | Origin country code (optional) |
| `destination` | Destination country code (optional) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/queue?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#queue)

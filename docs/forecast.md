# Queue Forecast API

ML ensemble forecast of queue levels: 24-hour and 7-day (168h) horizons with confidence bounds. The same model that powers nakordoni.eu predictions.

**Endpoint:** `GET /api/v1/data/forecast?ppid=id_13&prediction_steps=24`

> Counts against **forecast+stats** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `ppid` | Checkpoint ID |
| `prediction_steps` | 24 (default) or 168 for 7-day |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/forecast?ppid=id_13&prediction_steps=24" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "forecast",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#forecast)

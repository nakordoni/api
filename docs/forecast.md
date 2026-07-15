# Queue Forecast API

ML ensemble forecast of queue levels: 24-hour and 7-day (168h) horizons with confidence bounds. The same model that powers nakordoni.eu predictions.

**Endpoint:** `GET /api/v1/data/forecast`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID |
| `prediction_steps` | 24 (default) or 168 for 7-day |


## Example

```bash
curl "/api/v1/data/forecast?ppid=id_13&prediction_steps=24" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
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

Full docs: https://nakordoni.eu/en/developers/docs#forecast
*Auto-generated 2026-07-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
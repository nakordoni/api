# Bus Carrier Border Stats API

Border-crossing performance per bus carrier: crossings, average/median/min/max wait minutes — built from our own plate-matched crossing records.

**Endpoint:** `GET /api/v1/data/bus-carriers`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID or "all" for aggregated |
| `days` | Period 1-90 (default 30) |
| `min_crossings` | Minimum crossings to include a carrier (default 3) |
| `limit` | Max carriers returned (default 20) |


## Example

```bash
curl "/api/v1/data/bus-carriers?ppid=all&days=30" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
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

Full docs: https://nakordoni.eu/en/developers/docs#bus-carriers
*Auto-generated 2026-07-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
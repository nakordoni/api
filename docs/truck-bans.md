# Truck Driving Bans API

European truck driving restrictions by country and date, including seasonal and holiday bans.

**Endpoint:** `GET /api/v1/data/truck-bans`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO country code (optional) |
| `date` | YYYY-MM-DD (optional) |


## Example

```bash
curl "/api/v1/data/truck-bans?country=PL" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "truck-bans",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#truck-bans
*Auto-generated 2026-06-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Truck Driving Bans API

European truck driving restrictions by country and date, including seasonal and holiday bans. Each ban carries its restriction type (General / Local / Sunday / Holiday / Seasonal), the exact scope or roads affected and the minimum weight, and each country a live status computed in its own timezone (active_window / next_window), plus a covered_countries list.

**Endpoint:** `GET /api/v1/data/truck-bans`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO country code (optional) |
| `date` | YYYY-MM-DD (optional) |
| `lang` | Language code for country names and summary (default en) |
| `include_ua_heat` | 1 = also return Ukraine's computed summer heat ban (trucks >24 t banned 10:00-22:00 when forecast temp >+28 °C), per oblast. Included automatically when Ukraine is in scope (country=UA, a Ukrainian border ppid, or no filter at all) — this flag only adds it to a request scoped to some other country |


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
*Auto-generated 2026-08-13 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
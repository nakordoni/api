# Truck Driving Bans API

European truck driving restrictions for one or more countries, including seasonal and holiday bans. Each ban carries its restriction type (General / Local / Sunday / Holiday / Seasonal), the exact scope or roads affected and the minimum weight, and each country a live status computed in its own timezone (active_window / next_window), plus a covered_countries list. ?country= accepts a comma-separated list of up to 10 codes; it is optional in v1 and required in v2. The response also reports `returned`, `total_available` and `truncated` so a capped answer is never mistaken for a complete one.

**Endpoint:** `GET /api/v1/data/truck-bans`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code, or a comma-separated list of up to 10, e.g. RO or DE,RO. Optional in v1, where an unscoped call returns a capped slice — check truncated. Required in v2. |
| `lang` | Language code for country names and summary (default en) |
| `include_ua_heat` | 1 = also return Ukraine's computed summer heat ban (trucks >24 t banned 10:00-22:00 when forecast temp >+28 °C), per oblast. Included automatically when Ukraine is in scope (country=UA) — this flag only adds it to a request scoped to some other country |


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
*Auto-generated 2026-08-24 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
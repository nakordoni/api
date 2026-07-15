# Holiday Calendar API

Official public holidays per European country — dates, local names and type. Backed by the same Nager.Date / OpenHolidaysAPI service (7-day cache, locally-computed Kosovo calendar) that powers the nakordoni.eu holiday calendar page and the prediction system's calendar factors. Country mode returns the full-year list; upcoming mode returns a flat cross-country list within N days; default mode indexes a core country set with each next holiday.

**Endpoint:** `GET /api/v1/data/holiday-calendar`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code for the full-year list of one country, e.g. PL (optional — omit for the index mode) |
| `year` | YYYY (default: current year) |
| `upcoming` | 1 = flat list of upcoming holidays across countries within `days` (optional) |
| `days` | Horizon in days for upcoming mode, 1-180 (default 30) |
| `countries` | Comma-separated ISO-2 list to scope index/upcoming mode, max 15 (default: UA,PL,SK,HU,RO,MD,DE,CZ,BG,AT) |
| `lang` | Language for the localized `name` field (default en) — same 24-language translator as the nakordoni.eu holiday calendar page. Falls back to the local/English name for anything not yet cached. |


## Example

```bash
curl "/api/v1/data/holiday-calendar?country=PL&year=2026&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "holiday-calendar",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#holiday-calendar
*Auto-generated 2026-07-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
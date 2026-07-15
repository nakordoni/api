# Holiday Calendar API

Official public holidays per European country — dates, local names and type, each country's full holiday list included. Backed by the same Nager.Date / OpenHolidaysAPI service (7-day cache, locally-computed Kosovo calendar) that powers the nakordoni.eu holiday calendar page and the prediction system's calendar factors. One country -> a flat object; several -> the same shape wrapped in a list. upcoming=1 switches to a flat, date-sorted cross-country feed; compare_to switches to a same-vs-different comparison across countries — useful for checking whether a date is a holiday on both sides of a border crossing or just one.

**Endpoint:** `GET /api/v1/data/holiday-calendar`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code, or comma-separated list of up to 15, e.g. PL or PL,DE,UA. Omit for the default core set (UA,PL,SK,HU,RO,MD,DE,CZ,BG,AT). One resolved country returns a flat object; several return a list. |
| `compare_to` | ISO-2 country code, or comma-separated list, to compare against `country`. Switches to compare mode: returns which holiday dates are shared by ALL selected countries (`same`) vs only some of them (`different`, with per-date in/not_in lists) |
| `year` | YYYY (default: current year) |
| `upcoming` | 1 = switch to a flat, date-sorted list of upcoming holidays across the selected countries within `days` (optional) |
| `days` | Horizon in days for upcoming mode, 1-180. Omitted or 0 = no limit (everything the underlying service can see: this year + next) |
| `lang` | Language for the localized `name` field (default en) — same 24-language translator as the nakordoni.eu holiday calendar page. Falls back to the local/English name for anything not yet cached. |


## Example

```bash
curl "/api/v1/data/holiday-calendar?country=PL&compare_to=UA&year=2026&lang=en" \
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
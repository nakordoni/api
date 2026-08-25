# Truck Driving Bans API

European truck driving restrictions for one or more countries, including seasonal and holiday bans. Each ban carries its restriction type (General / Local / Sunday / Holiday / Seasonal), the exact scope or roads affected and the minimum weight, and each country a live status computed in its own timezone (active_window / next_window), plus a covered_countries list. ?country= accepts a comma-separated list of up to 3 codes; it is optional in v1 and required in v2. The response also reports `returned`, `total_available` and `truncated` so a capped answer is never mistaken for a complete one, plus `window` {from,to,days} naming the exact date range it covers. v1 always covers the next 7 days; v2 additionally accepts ?date= / ?date_from= + ?date_to= for any window up to 92 days, beginning at most 7 days in the past (this is a forward-looking calendar — older history is refused with 400 date_too_old) and running forward to 2028-12-31.

**Endpoint:** `GET /api/v1/data/truck-bans`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code, or a comma-separated list of up to 3, e.g. RO or DE,RO. More than 3 is refused with 400 too_many_countries — split the request. Optional in v1, where an unscoped call returns a capped slice — check truncated. Required in v2. |
| `lang` | Language code for country names and summary (default en) |
| `include_ua_heat` | 1 = also return Ukraine's computed summer heat ban (trucks >24 t banned 10:00-22:00 when forecast temp >+28 °C), per oblast. Included automatically when Ukraine is in scope (country=UA) — this flag only adds it to a request scoped to some other country |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/truck-bans?country=PL" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.current_bans[]` | Bans in force right now, same fields as upcoming_bans. is_ban_active is the quick boolean for 'may a truck drive at this moment'. |
| `data.upcoming_bans[].date` | Ban day, with time_from and time_until in local time. |
| `data.upcoming_bans[].country_code` | Country the ban applies in, with country_name. |
| `data.upcoming_bans[].restriction_type` | What kind of ban it is (weekend, holiday, heat…), with restriction_details in the requested language. |
| `data.upcoming_bans[].min_weight_tons` | Weight from which the ban bites — a lighter truck is unaffected. |
| `data.upcoming_bans[].details_url` | Official source page for that ban. |
| `data.bans_by_country` | The same bans grouped by ISO-2 country, for rendering per country. |
| `data.window` | The period covered: from, to, days and whether you requested it. |
| `data.total_bans` | Bans in the window, with returned, total_available and truncated — true means narrow the window or page. |
| `data.covered_countries` | Countries we track bans for; countries_not_covered names the ones you asked about that we do not. |
| `data.summary` | One-sentence plain-language summary, ready to show a driver. as_of is when it was assembled. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
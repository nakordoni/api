# Holiday Calendar — Holidays API

Public, bank, regional, religious and observance holidays for 51 countries (Europe + Gulf) and their regions, 2020-2035, from our own holiday rule engine. Every holiday says whether it is a day off, a bank holiday or inside school holidays, whether a weekend holiday moved to another day, whether its date is still estimated (Islamic calendar), and whether a truck driving ban applies that day and when. Covers Ukraine's martial-law rule (holidays listed, not days off) and decree day transfers (transfers[]). Up to 10 countries per call; a year or any range up to 731 days.

**Endpoint:** `GET /api/v2/data/holiday-calendar-holidays`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code, or comma-separated list of up to 10 (required), e.g. DE or DE,PL,UA |
| `year` | YYYY, 2020-2035 (default: current year). Ignored when from/to is sent |
| `from` | Range start YYYY-MM-DD (with to; at most 731 days) |
| `to` | Range end YYYY-MM-DD |
| `region` | ISO 3166-2 region, e.g. DE-BY (or BY with one country): national holidays + that region's only. Omit for every region |
| `type` | Comma-separated filter: PUBLIC, BANK, REGIONAL, RELIGIOUS, OBSERVANCE, SCHOOL, OPTIONAL, HALF_DAY, BRIDGE, or all. Default: everything except BRIDGE |
| `lang` | Comma-separated language codes for name.translations (default: every language we hold), e.g. en,uk,pl |
| `q` | Search in holiday names (any language), e.g. christmas |
| `id` | One holiday by id (other filters ignored) |
| `school` | 1 = also list school-holiday periods (school_holidays[]) for countries that publish them |
| `include` | impact = add business_intelligence to each holiday (Pro and higher) |
| `min_impact` | Only holidays with an impact_score of at least this (0-100); implies include=impact |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/holiday-calendar/holidays?country=DE&year=2026&region=DE-BY&lang=en,de" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.holidays[].id` | Stable holiday id, e.g. hol_de_christmas_day_2026 — the value ?id= takes. Observed (moved) days end in _obs. |
| `data.holidays[].date` | iso (YYYY-MM-DD), year, month, day, day_of_week (ISO 1=Monday..7=Sunday), day_name, is_weekend (country weekend). |
| `data.holidays[].name` | en, local (in local_lang) and translations {lang: name}. |
| `data.holidays[].location` | country_code, scope NATIONAL or REGIONAL, regions_affected (ISO 3166-2 codes). |
| `data.holidays[].classification` | type, is_day_off, is_bank_holiday, is_school_holiday, is_bridge, estimated (computed Islamic-calendar date), note (suspended_martial_law, transferred_by_decree or null). |
| `data.holidays[].floating_logic` | is_floating, rule (plain-language date rule), shifted_from (original date when a weekend holiday moved). |
| `data.holidays[].truck_ban` | Holiday truck driving ban in that country: applies, time_from, time_until (local time), min_weight_tons, eve_rule {date,time_from,time_until} for a ban on the eve, scope_note. |
| `data.holidays[].business_intelligence` | With include=impact on Pro and higher: the same model-estimated block the impact product returns. |
| `data.transfers[]` | Decree day transfers in the window: date, country_code, kind (day_off \| working_day), region, note. |
| `data.school_holidays[]` | With school=1: id, start_date, end_date, name, location. |
| `data.count` | Holidays returned; countries and window {from,to} echo the request. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "holiday-calendar-holidays",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#holiday-calendar-holidays
*Auto-generated 2026-10-28 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
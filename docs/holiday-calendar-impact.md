# Holiday Calendar — Business Impact API

Model-estimated business impact of holidays for retail, logistics and demand planning (Pro and higher). insights scores every holiday 0-100 with the probability that shops are closed, the demand categories that rise before it, long-weekend and bridge position and our border-traffic multiplier, plus a per-country summary. demand-surge returns the dated windows around day-off holidays (pre-holiday, holiday, post-holiday) with an intensity each. Scores are estimates from holiday type, day-off status, scope, calendar position, truck bans and our traffic multipliers — not observed sales data.

**Endpoint:** `GET /api/v2/data/holiday-calendar-impact`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `op` | insights (default) \| demand-surge |
| `country` | ISO-2 country code, or comma-separated list of up to 10 (required) |
| `year` | YYYY, 2020-2035 (default: current year) |
| `from` | Range start YYYY-MM-DD (with to; at most 731 days) |
| `to` | Range end YYYY-MM-DD |
| `region` | ISO 3166-2 region, e.g. DE-BY (optional) |
| `lang` | Language codes for names (default: all held) |
| `min_impact` | Only holidays with an impact_score of at least this (0-100) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/holiday-calendar/impact?op=insights&country=DE,PL&year=2026" \
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
| `data.holidays[].business_intelligence` | impact_score 0-100, retail_closure_probability 0-1, demand_surge_categories[], long_weekend, bridge_opportunity, holiday_family, border_traffic_multiplier (null when none), basis model_estimated, model. |
| `data.summary[]` | op=insights, per country: holidays, weekday_days_off, long_weekends, average_impact_score, top_holidays[3]. |
| `data.windows[]` | op=demand-surge: date, phase (pre_holiday \| holiday \| post_holiday), intensity 0-100, demand_surge_categories, retail_closure_probability (holiday phase), holiday_id, holiday_name, country_code, regions_affected. |
| `data.impact_model` | Model id and the note that these are estimates. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "holiday-calendar-impact",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#holiday-calendar-impact
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
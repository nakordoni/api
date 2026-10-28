# Holiday Calendar — Countries API

The 51 countries (Europe + Gulf) the holiday calendar covers, each with its weekend days, time zone, regions, holiday types and whether school holidays are included. With ?country= one country in detail: working days per year 2020-2035, holidays and days off per year, decree day transfers and the next five holidays.

**Endpoint:** `GET /api/v2/data/holiday-calendar-countries`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | ISO-2 country code for the detail view (optional; omit for the list) |
| `lang` | Language codes for next_holidays names (detail view; default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/holiday-calendar/countries?country=DE" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.countries[]` | List view: country_code, name, timezone, local_lang, weekend_days[{iso,name}], regions[{code,name,type}], holiday_types, has_school_holidays, has_estimated_dates, day_off_suspensions, coverage. |
| `data.country` | Detail view: the same fields for one country, plus the ones below. |
| `data.country.business_days_by_year` | Working days per year (national calendar). |
| `data.country.holidays_by_year` | Per year: holidays listed and days off. |
| `data.country.next_holidays[]` | The next five holidays from today, in the holidays product shape. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "holiday-calendar-countries",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#holiday-calendar-countries
*Auto-generated 2026-10-28 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
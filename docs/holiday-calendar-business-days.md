# Holiday Calendar — Business Days API

Business-day arithmetic for one country, optionally one region: count working days between two dates, add or subtract working days, test a date, or find the next / previous working day. Honours the country's own weekend, national and regional day-off holidays, decree transfers (a Saturday that is worked) and Ukraine's martial-law rule. Dates 2020-01-01 to 2035-12-31. Answers from a precomputed calendar in a few milliseconds.

**Endpoint:** `GET /api/v2/data/holiday-calendar-business-days`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `op` | count (default) \| add \| subtract \| is \| next \| prev |
| `country` | ISO-2 country code (required, one) |
| `region` | ISO 3166-2 region, e.g. DE-BY (optional) |
| `start_date` | YYYY-MM-DD (required) |
| `end_date` | YYYY-MM-DD (op=count) |
| `days` | Working days to add or subtract, 0-3650 (op=add\|subtract); 0 = start_date if it is a working day, else the next (add) or previous (subtract) one |
| `inclusive` | op=count: true (default) counts start_date and end_date; false leaves start_date out |
| `weekend` | Optional weekend override, e.g. fri,sat or 5,6 (ISO weekdays); none = no weekend days |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/holiday-calendar/business-days?op=count&country=DE&region=DE-BY&start_date=2026-01-01&end_date=2026-12-31" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.business_days` | op=count: working days in the range, with calendar_days and non_business_days. |
| `data.result_date` | op=add\|subtract\|next\|prev: the resulting date. |
| `data.is_business_day` | op=is: true or false, with reasons[] (weekend, holiday: <name>, day off by decree transfer) when false. |
| `data.weekend_days` | Present when ?weekend= overrode the country weekend. |
| `data.calendar_window` | First and last date the calendar covers. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "holiday-calendar-business-days",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#holiday-calendar-business-days
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
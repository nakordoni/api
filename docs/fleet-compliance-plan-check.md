# Fleet Compliance — Plan Check API

Check planned shifts or trip days before dispatch. Send the shifts and the countries they run in as a JSON POST body; each shift comes back with the truck driving bans it hits (weekend, Sunday, holiday and other bans from our truck-ban calendar, filtered by vehicle weight), the public holidays and weekend days it touches, the EU 561 / AETR and 2002/15 breaches the plan would cause, and the earliest legal start: after the required daily or weekly rest and outside every blocking ban.

**Endpoint:** `GET /api/v2/data/fleet-compliance-plan-check`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `shifts` | (JSON body, required, max 60, span max 62 days) [{start, end, countries?, drive_min?, other_work_min?, activities?}]. Without activities the shift is laid out as other work, then driving in 4 h 30 blocks with 45 min breaks. Each activity: {type: drive \| other_work \| availability \| break \| rest, start, end} — ISO-8601 (a datetime without offset is read in tz) or unix seconds. Optional on rests: ferry (true on a ferry or train), sleeper_cabin, scheduled_journey_h, in_vehicle, location (residence \| operational_centre \| elsewhere — EU 561 Art. 8(8a) 4-week return check). Optional on drives: art12_exceed (true = driving past the limit under EU 561 Art. 12 to reach the operational centre or home for a weekly rest). Gaps between activities count as rest (reported in warnings); overlaps are rejected. |
| `countries` | (JSON body) ISO country codes used for shifts that do not list their own, e.g. ["DE","AT"]. |
| `tz` | (JSON body) IANA time zone of the plan (datetimes without offset), default UTC. Ban windows use each country's own time zone. |
| `weight_t` | (JSON body) Vehicle weight in tonnes; bans with a weight threshold at or above it are skipped. Omitted = every ban is included. |
| `history` | (JSON body) Recent activities before the plan (format as in validate) so rests and budgets carry over. |
| `last_weekly_rest_end` | (JSON body) End of the last weekly rest before the plan. |
| `ruleset` | (JSON body) eu561 (default) or aetr. crew single \| double. international_goods, include_wtd, wtd as in validate. |
| `allow_reduced_daily_rest` | (JSON body) true = suggestions may use a 9 h reduced daily rest (default: 11 h). |
| `allow_reduced_weekly_rest` | (JSON body) true = suggestions may use a 24 h reduced weekly rest (default: 45 h). |
| `blocking_types` | (JSON body) Ban types that move a suggested start (default Sunday, Saturday, Holiday, Holiday Eve, General; nationwide bans only). Others are still flagged. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fleet/compliance/plan-check" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.shifts[]` | Per shift: index, start, end, countries, duration_min, flags[], violations[], suggested_start, suggested_end, delay_min, delay_reasons[] (daily_rest, weekly_rest, truck_ban:<CC>:<type>). |
| `data.shifts[].flags[]` | type truck_ban (country, date, restriction_type, from, until, scope, details, min_weight_t, blocking, overlap_min), public_holiday (country, date, names, truck_ban_recorded) or weekend (country, date, day). |
| `data.violations[]` | Every EU 561 / AETR / 2002/15 breach of the plan, in the validate format. |
| `data.remaining_after_plan` | Driving budgets and next rests after the last planned shift (validate remaining.driving). |
| `data.summary` | shift_count, shifts_flagged, shifts_moved, violation_count, ban_windows_considered, countries, ruleset, crew. |
| `data.warnings[]` | e.g. shift_layout_overflows, holidays_unavailable; data.assumptions[] says how missing input was read. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fleet-compliance-plan-check",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fleet-compliance-plan-check
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
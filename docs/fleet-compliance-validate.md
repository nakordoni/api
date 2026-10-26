# Fleet Compliance — Validate API

Check a driver's recorded activity against EU 561/2006 (or AETR) driving and rest rules and Directive 2002/15/EC working time. Send the timeline as a JSON POST body — the current shift plus up to 28 days or more of tachograph-like history — and get every violation with its rule article, timestamp and seriousness (Regulation (EU) 2016/403 bands), warnings, and what the driver has left right now: daily driving, 56 h weekly and 90 h fortnight budgets, when the next break, daily rest and weekly rest are due, extended days and reduced rests used, and any reduced weekly rest compensation owed with its deadline. Covers 4 h 30 breaks (incl. 15 + 30 split), 9/10 h days, 11/9 h and 3 + 9 h daily rest, six 24-hour periods between weekly rests, reduced weekly rest sequences and compensation, double crew (9 h rest within 30 h), ferry/train interrupted rest, and 2002/15 48 h average, 60 h week, breaks after 6 h and the 10 h night-work cap. route_plan_state can be passed straight to route-plan.

**Endpoint:** `GET /api/v2/data/fleet-compliance-validate`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `activities` | (JSON body, required) Driver activities, oldest first or in any order. Each activity: {type: drive \| other_work \| availability \| break \| rest, start, end} — ISO-8601 (a datetime without offset is read in tz) or unix seconds. Optional on rests: ferry (true on a ferry or train), sleeper_cabin, scheduled_journey_h, in_vehicle. Gaps between activities count as rest (reported in warnings); overlaps are rejected. |
| `history` | (JSON body) Older activities in the same format (e.g. the last 28 days from the tachograph); merged with activities. |
| `tz` | (JSON body) IANA time zone for calendar weeks (Monday 00:00) and the night window, default UTC. |
| `ruleset` | (JSON body) eu561 (default) or aetr. limits lists the AETR differences that are applied. |
| `crew` | (JSON body) single (default) or double (multi-manning). |
| `now` | (JSON body) Evaluation time, default the end of the last activity; time after the last activity counts as rest. |
| `last_weekly_rest_end` | (JSON body) End of the last weekly rest before the timeline, when the timeline does not contain it. |
| `international_goods` | (JSON body, eu561) true = the driver is in international goods transport outside the Member State of establishment (two consecutive reduced weekly rests allowed under Art. 8(6)). |
| `include_wtd` | (JSON body) false = skip the Directive 2002/15/EC checks (default true). |
| `wtd` | (JSON body) 2002/15 options: reference_weeks 1-52 (default 17), reference_mode rolling \| fixed, reference_start, night_window HH:MM-HH:MM (default 00:00-04:00), country. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fleet/compliance/validate" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.compliant` | true when no violation was found; violation_count and by_severity (most_serious, very_serious, serious, minor) count them. |
| `data.violations[]` | code, ref (rule article, e.g. "EU 561/2006 Art. 7"), severity, at (when the limit was passed), value_min / limit_min / excess_min, period_start or week_start, open (still running at evaluation time), message. 2002/15 entries carry banded=false. |
| `data.warnings[]` | Things to act on that are not violations yet: code, message, optional ref and at (e.g. extended_day_in_use, weekly_drive_almost_used, history_short, gaps_counted_as_rest, four_week_rule_pending). |
| `data.remaining.driving` | drive_left_now_min (the smallest of the daily, weekly and fortnight budgets), daily_drive_left_min, weekly_drive_left_min, fortnight_drive_left_min, next_break (due_after_drive_min, required_min, split_first_part_taken), next_daily_rest (regular_latest_start, reduced_latest_start, min_duration_min, overdue), next_weekly_rest (latest_start, periods_used, min_duration_min, reduced_allowed), extended_days_used_this_week, reduced_daily_rests_used, reduced_weekly_rest_compensation[] (owed_min, deadline), compensation_owed_min. |
| `data.remaining.working_time` | 2002/15: week_work_left_min, worked_this_week_min, reference_period (average, budget_left_min), shift (work_min, breaks_min, next_break_due_after_work_min, night_work_in_shift, shift_work_left_min). |
| `data.route_plan_state` | Driver state in route-plan parameter names (driven_since_break_min, driven_today_min, duty_started_min_ago, driven_week_min, driven_fortnight_min, extended_days_used, reduced_rests_used). |
| `data.duty_periods[]` | Each duty period between daily/weekly rests: start, end, drive_min, extended, rest_type, rest_start, rest_end, rest_min (+ ferry_interruptions). |
| `data.weekly_rests[]` | Weekly rests found: start, end, duration_min, type regular \| reduced, periods_since_previous, compensation_owed_min, derogation. |
| `data.weeks` | Per calendar week: driving.drive_min and working_time.work_min, with complete. |
| `data.rolling` | Information only: the most driving in any 168 h and 336 h window (the legal test uses calendar weeks). |
| `data.assumptions[]` | How incomplete input was read (e.g. no weekly rest before the timeline known). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fleet-compliance-validate",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fleet-compliance-validate
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
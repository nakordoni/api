# Fleet Compliance — Limits API

The rule parameters the compliance checks use, as JSON, so an app can show them: EU 561/2006 (as amended by 2020/1054), AETR with its verified differences from 561, and Directive 2002/15/EC with its options, plus the Regulation (EU) 2016/403 seriousness bands. Static; cache it.

**Endpoint:** `GET /api/v2/data/fleet-compliance-limits`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ruleset` | Optional: eu561, aetr or wtd — return one ruleset only. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fleet/compliance/limits?ruleset=aetr" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.rulesets.eu561` | name, source, params (minutes: break_after_drive_min 270, daily_drive_min 540, weekly_drive_min 3360, …), notes[]. |
| `data.rulesets.aetr` | Same params plus differences_vs_eu561_verified[] (topic, aetr, eu561), same_as_eu561, amendments_after_2010_checked (source, result, encoded[]) and unverified_todo[]. |
| `data.rulesets.wtd_2002_15` | params (avg_week_max_min 2880, week_max_min 3600, reference_weeks, night window, break rules) and options. |
| `data.severity` | Seriousness scale and the 2016/403 Annex I bands per infringement type. |
| `data.activity_types` | Accepted activity types and rest flags for validate and plan-check. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fleet-compliance-limits",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fleet-compliance-limits
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
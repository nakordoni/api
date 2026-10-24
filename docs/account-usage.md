# Account Usage API

What is left of your limits right now, for the key you call with: plan, per-second cap, and for each daily quota class (cheap, heavy, route) the limit, units used, units remaining and when the counter resets (midnight UTC). Also your approved countries as this key sees them and where to upgrade. Not metered — reading it never uses quota — and limited to 30 calls per minute per key. The numbers are the same ones the X-Devapi-Limit / X-Devapi-Remaining headers carry on every metered call.

**Endpoint:** `GET /api/v2/data/account-usage`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "https://nakordoni.eu/api/v2/account/usage" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.plan` | Your plan: slug and display name. |
| `data.qps` | Per-second request cap that applies to this key now. |
| `data.quotas.cheap.limit` | Daily allowance of the standard class; heavy and route have the same four fields. |
| `data.quotas.cheap.used` | Units spent today with this key. |
| `data.quotas.cheap.remaining` | Units left today — equals X-Devapi-Remaining. |
| `data.quotas.cheap.reset_at` | When the counter resets, ISO-8601 UTC (midnight UTC). The X-Devapi-Reset header gives the same moment as a Unix timestamp. |
| `data.markets.approved` | Countries this key may use. Countries switched on through an integration work only with that integration's key. |
| `data.markets.allowed` | How many countries the plan allows; null = unlimited. |
| `data.markets.available` | Integration keys only: the integration's countries you do not have yet (null for other keys). |
| `data.markets.key_bound` | true when some approved countries belong to one key only, so another key can see a different list. |
| `data.upgrade_url` | Billing page in your account language — upgrade the plan or add extra countries. |
| `data.pass` | Integration keys only: Trip Pass state {active, expires_at, days_left}; null for other keys. |
| `data.pass_url` | Integration keys only: link to the Trip Pass on the Billing page (account language); null for other keys. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "account-usage",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#account-usage
*Auto-generated 2026-10-24 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
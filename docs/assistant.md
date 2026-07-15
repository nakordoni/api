# Border AI Assistant API

Ask our production AI assistant any border-crossing question (queues, forecasts, rules, fuel, routes) and get the same grounded answer that powers the nakordoni.eu widget — in 24 languages. Already used in production by yaknakordoni.com.ua.

**Endpoint:** `GET /api/v1/data/assistant`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `q` | The question (plain text) |
| `lang` | Answer language (default en) |
| `ppid` | Checkpoint context, e.g. id_13 (optional) |


## Example

```bash
curl "/api/v1/data/assistant?q=How long is the queue at Krakovets now?&lang=en&ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "assistant",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#assistant
*Auto-generated 2026-07-15 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
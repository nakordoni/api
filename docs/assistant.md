# Border AI Assistant API

Ask our production AI assistant any border-crossing question (queues, forecasts, rules, fuel, routes) and get the same grounded answer that powers the nakordoni.eu widget — in 24 languages. Already used in production by yaknakordoni.com.ua.

**Endpoint:** `GET /api/v1/data/assistant`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `q` | The question (plain text) |
| `lang` | Answer language (default en) |
| `ppid` | Checkpoint context, e.g. id_13 (optional) |


## Example

```bash
curl "https://nakordoni.eu/api/v1/data/assistant?q=How long is the queue at Krakovets now?&lang=en&ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.question` | The question as asked, with lang and ppid — the checkpoint context the answer was grounded on. |
| `data.answer_text` | The answer, in the requested language. Structured answers add their own fields beside it (summary, figures, sources) depending on the question type. |
| `usage` | Envelope level: limit, used, reset — this product is metered like any other. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
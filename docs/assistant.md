# Border AI Assistant API

Ask our production AI assistant any border-crossing question (queues, forecasts, rules, fuel, routes) and get the same grounded answer that powers the nakordoni.eu widget — in 24 languages. Already used in production by yaknakordoni.com.ua.

**Endpoint:** `GET /api/v1/data/assistant?q=How long is the queue at Krakovets now?&lang=en&ppid=id_13`

> Counts against **forecast+stats** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `q` | The question (plain text) |
| `lang` | Answer language (default en) |
| `ppid` | Checkpoint context, e.g. id_13 (optional) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/assistant?q=How long is the queue at Krakovets now?&lang=en&ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
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

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#assistant)

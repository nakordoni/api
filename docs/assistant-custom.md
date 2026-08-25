# Personalised AI Assistant API

Your own AI assistant, grounded on YOUR content plus OUR live border data. Point it at your markdown files or let us fetch the pages you name — we index them and answer from them. Choose which of our data feeds it may use (queue, forecast, alternatives, fuel, truck bans, holidays, road conditions…), choose the model tier (that is what sets the price), write your own instructions with {{feed.slug}} placeholders saying exactly where our data goes in the answer, and add a closing sentence of your own that is appended to every reply. Build it and test it at /{lang}/developers/studio, then call it here. Price per answer = model tier units + 1 unit per enabled feed (shown in the studio and in X-Devapi-Units).

**Endpoint:** `GET /api/v2/data/assistant-custom`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `assistant_id` | Your assistant id from Developers → AI Studio (required) |
| `q` | The end-user question, plain text, max 1000 chars (required) |
| `lang` | Answer language code — overrides the assistant default (optional) |
| `ppid` | Checkpoint context for queue/forecast/alternatives feeds, e.g. id_13 (optional) |
| `origin` | Origin country code for the border feed (optional) |
| `destination` | Destination country code for the border feed (optional) |
| `crossing_type` | Vehicle type for border/alternatives feeds: 4=car, 6=bus, 8=truck<7.5t, 9=truck (optional) |
| `country` | Country context for fuel / truck-bans / holidays / road-conditions feeds (optional) |
| `lat` | Latitude for the POI feed (optional) |
| `lon` | Longitude for the POI feed (optional) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/assistant-custom?assistant_id=1&q=Should I cross tonight or in the morning?&ppid=id_13&lang=uk" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.assistant_id` | Which of your assistants answered, with assistant — its name. |
| `data.question` | The question as asked, with lang. |
| `data.answer` | The answer, grounded on your indexed content plus the feeds you enabled. |
| `data.model_tier` | Model tier that ran — this is what sets the price, together with the feed count. |
| `data.sources` | Your content chunks the answer drew on, so you can cite them. |
| `data.feeds` | Our live feeds that were actually used in this answer. |
| `data.feeds_skipped` | Feeds enabled on the assistant but skipped because the request lacked their context — each value names the parameters to pass next time. |
| `data.ms` | How long the answer took to generate. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "assistant-custom",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#assistant-custom
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
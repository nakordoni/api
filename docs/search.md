# Checkpoint Search API

Find checkpoint PPIDs by name. Pass a single name or a comma-separated list (up to 20). Searches all translation languages; returns all PPIDs at that location grouped by vehicle type (4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck). Use this to quickly discover the right ppid before calling the queue or forecast APIs.

**Endpoint:** `GET /api/v1/data/search`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `name` | Checkpoint name or comma-separated names, in any language (e.g. Krakovets, Краківець, Krakivets) |
| `lang` | Language for returned names (default en) |


## Example

```bash
curl "/api/v1/data/search?name=Krakovets,Shehyni&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "search",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#search
*Auto-generated 2026-06-23 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
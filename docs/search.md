# Checkpoint Search API

Find checkpoint PPIDs by name. Pass a single name or a comma-separated list (up to 20). Searches all translation languages; returns all PPIDs at that location grouped by vehicle type (4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck). Use this to quickly discover the right ppid before calling the queue or forecast APIs.

**Endpoint:** `GET /api/v1/data/search?name=Krakovets,Shehyni&lang=en`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `name` | Checkpoint name or comma-separated names, in any language (e.g. Krakovets, Краківець, Krakivets) |
| `lang` | Language for returned names (default en) |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/search?name=Krakovets,Shehyni&lang=en" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
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

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#search)

# Bus Sun Side — Bus Catalogue API

The bus models bus-sun-side accepts (generic vehicle classes, not specific makes) with their seat layout, rows, door side, windows and deck, plus the allowed values of every bus parameter. Static; cache it.

**Endpoint:** `GET /api/v2/data/bus-sun-catalog`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `bus_model` | Optional: return one model only (404 unknown_bus_model if the id is not in the catalogue). |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/bus-sun-catalog" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.version` | Catalogue version date. |
| `data.default_model` | standard_coach — used when no bus_model is sent. |
| `data.params` | Allowed values: layout[], rows {min, max, custom_min}, door_side[], windshield[], rear_window[], window_height[], deck[], mid_door[]. |
| `data.models` | Map id → title, layout, rows, door_side, windshield, rear_window, window_height, deck, mid_door, typical_seats. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "bus-sun-catalog",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#bus-sun-catalog
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
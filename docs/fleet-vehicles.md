# Fleet Vehicles API

Your own NakBus Live fleet: every vehicle registered to your company with plate, label, assigned route, public-map flag, status, whether a driver device is currently on shift, and the last measured timetable delay. Owner-scoped — the key sees its own fleet only, including vehicles hidden from the public bus map. Activate fleet tracking at nakordoni.eu/en/developers/fleet (first bus free).

**Endpoint:** `GET /api/v2/data/fleet-vehicles`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---



## Example

```bash
curl "https://nakordoni.eu/api/v2/data/fleet/vehicles" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.company` | Your fleet company: company_id, name, status, public_map. |
| `data.vehicles[].id` | Vehicle ID — the value fleet-history's vehicle_id takes. plate and label identify it. |
| `data.vehicles[].route_id` | Route it is assigned to, with route_name. |
| `data.vehicles[].on_shift` | Whether it is currently in service, with status and public — whether it appears on your public map. |
| `data.vehicles[].last_delay_min` | Its last known delay, with last_delay_at. |
| `data.count` | Vehicles returned. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "fleet-vehicles",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#fleet-vehicles
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
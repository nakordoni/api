# Route Planner API

A door-to-door plan for a border trip, not just a driving time. Returns the route, the crossings actually on it with their live or arrival-time-forecast queue, and the stops a driver really makes — rest breaks, a meal, refuelling — laid out on one timeline. The border is part of that timeline: a long queue satisfies the break that was coming up and resets the driving clock, so a 3-hour wait is never reported as 3 hours PLUS a full set of breaks nobody took. Car stops follow a driving-hygiene model; bus and truck stops respect the mandatory EU 561/2006 rest, and the bus service overhead is calibrated on 1000+ licensed international coach schedules. Set stop_places=1 to name a real rest area or fuel station for each stop. Each border also reports wait_basis: vehicle_lane when your vehicle class has its own measured queue, car_lane when the car-lane queue is the best available signal for that crossing.

**Endpoint:** `GET /api/v1/data/route-plan`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `origin_lat` | Origin latitude (required) |
| `origin_lon` | Origin longitude (required) |
| `dest_lat` | Destination latitude (required) |
| `dest_lon` | Destination longitude (required) |
| `vehicle` | car (default), bus or truck — picks both the routing profile and the stop model |
| `depart` | ISO-8601 departure time, up to 14 days ahead (default: now). Drives the arrival-time queue forecast |
| `via` | Optional waypoints as lat,lon;lat,lon (max 3) — e.g. to route through a different crossing |
| `forecast` | 1 (default) = forecast the queue for your arrival time at each crossing; 0 = use the current queue |
| `stop_places` | 1 = name a real rest area / fuel station for each stop (default 0) |
| `overnight` | 1 = add an advisory overnight stop when the drive exceeds 11 h (default 0) |
| `geometry` | 1 = include the full route geometry (default 0) |
| `alternatives` | 1 (default) = include nearby alternative crossings with their queues |
| `preference` | recommended (default), fastest or shortest |
| `lang` | Language code for checkpoint names (default en) |


## Example

```bash
curl "/api/v2/data/route-plan?origin_lat=50.4501&origin_lon=30.5234&dest_lat=52.2297&dest_lon=21.0122&vehicle=car&stop_places=1&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "route-plan",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#route-plan
*Auto-generated 2026-08-19 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
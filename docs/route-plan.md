# Route Planner API

A door-to-door plan for a border trip, not just a driving time. Returns the route, the crossings actually on it with their live or arrival-time-forecast queue, and the stops a driver really makes — rest breaks, a meal, refuelling — laid out on one timeline. The border is part of that timeline: a long queue satisfies the break that was coming up and resets the driving clock, so a 3-hour wait is never reported as 3 hours PLUS a full set of breaks nobody took. Car stops follow a driving-hygiene model; bus and truck stops respect the mandatory EU 561/2006 rest, and the bus service overhead is calibrated on 1000+ licensed international coach schedules. Set stop_places=1 to name a real rest area or fuel station for each stop. Each border also reports wait_basis: vehicle_lane when your vehicle class has its own measured queue, car_lane when the car-lane queue is the best available signal for that crossing.

**Endpoint:** `GET /api/v2/data/route-plan`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

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
curl "https://nakordoni.eu/api/v2/data/route-plan?origin_lat=50.4501&origin_lon=30.5234&dest_lat=52.2297&dest_lon=21.0122&vehicle=car&stop_places=1&lang=en" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.route` | The drive itself: distance_km, drive_seconds, vehicle, toll_distance_km. |
| `data.borders[].ppid` | A crossing on the route, with name, from, to and crossing_type. |
| `data.borders[].wait` | Its live or arrival-time-forecast wait, with wait_basis — vehicle_lane when your vehicle class has its own measured queue, car_lane when the car queue is the best available signal. |
| `data.borders[].at_km` | Where it falls on the route, with at_drive_seconds and distance_from_route_km. |
| `data.borders[].alternatives[]` | Other crossings worth switching to: ppid, name, wait_minutes, queue_cars, detour_km. |
| `data.stops[].type` | Break, meal or refuelling, with reason, at_km, at_drive_s, minutes, starts_at and ends_at. |
| `data.summary` | The timeline totals: drive_seconds, stop_seconds, border_wait_seconds, total_seconds, arrival_at, stop_count, border_count and borders_absorbed_breaks — long waits that satisfied a due break instead of adding to it. |
| `data.model` | Rules the plan was built on: vehicle, basis (driving-hygiene or EU 561/2006), break_after_drive_min, meal_after_drive_min, fuel_every_km. |
| `data.warnings[]` | Things to tell the driver: code, ppid, name, status, message, suggested_alternative — e.g. a closed crossing on the route. |


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
*Auto-generated 2026-08-25 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
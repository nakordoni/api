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
| `rest_rules` | legacy (default in v2) or eu561 — with overnight=1 and vehicle=truck\|bus: 9 h driving per day, 11 h daily rest (type overnight, minutes 660), 45 min break after 4 h 30. eu561 is the default from v3 |
| `driven_since_break_min` | eu561 driver state: minutes driven since the last 45 min break (default 0) |
| `driven_today_min` | eu561: minutes driven since the last daily rest (default 0) |
| `duty_started_min_ago` | eu561: minutes elapsed since the last daily rest ended; the next daily rest must start within 13 h (15 h after a reduced rest) of that (default 0) |
| `driven_week_min` | eu561: minutes driven this week; at 56 h a weekly_rest stop (45 h) is added (default 0) |
| `driven_fortnight_min` | eu561: minutes driven in the fortnight; cap 90 h (default 0) |
| `extended_days_used` | eu561: 10 h driving days already used this week, max 2 (default 0). A 10 h day is planned only when it lets the trip end without another daily rest |
| `reduced_rests_used` | eu561: reduced 9 h daily rests used since the last weekly rest, max 3 (default 0) |
| `allow_reduced` | eu561: 1 = may use a 9 h reduced daily rest when it lets the trip end next day (default 0) |
| `places_before_min` | With stop_places=1: minutes of driving before each break or rest in which to look for places, 0–120 (default 45) |
| `places_after_min` | With stop_places=1: minutes of driving after each break or rest in which to look for places, 0–120 (default 15) |
| `places_radius_km` | With stop_places=1: max distance off the route for break and meal places, 0.5–25 km (default 5; above 25 it is clamped and warning radius_clamped is returned) |
| `rest_radius_km` | With stop_places=1: max distance off the route for overnight and weekly-rest places, 0.5–25 km (default 5; above 25 it is clamped with radius_clamped) |
| `geometry` | 1 = include the full route geometry (default 0) |
| `alternatives` | 1 (default) = include nearby alternative crossings with their queues |
| `preference` | recommended (default), fastest or shortest |
| `lang` | Language code for checkpoint names (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/route-plan?origin_lat=50.4501&origin_lon=30.5234&dest_lat=52.2297&dest_lon=21.0122&vehicle=car&stop_places=1&lang=en" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.route` | The drive itself: distance_km, drive_seconds, vehicle, toll_distance_km. |
| `data.borders[].ppid` | A crossing on the route, with name, from, to and crossing_type. |
| `data.borders[].wait` | Its live or arrival-time-forecast wait, with wait_basis — vehicle_lane when your vehicle class has its own measured queue, car_lane when the car queue is the best available signal. |
| `data.borders[].registration_required` | true on a Ukraine-exit truck lane with an electronic queue (eCherha): register before arriving. |
| `data.borders[].registration_wait_minutes` | eCherha published wait in minutes for that lane (fresh data only, never capped), else null. With rest_rules=eu561 (default from v3) it is used as the border wait (wait.source echerha_registration) and as a rest; with legacy it is reported only. Warning echerha_registration when above 0. |
| `data.borders[].registration_wait_source` | echerha when registration_wait_minutes is set, else null. |
| `data.borders[].registration_wait_at` | ISO 8601 UTC time of the eCherha reading. |
| `data.borders[].registration_note` | Localized (lang) note about registering on eCherha; registration_url is https://echerha.gov.ua. Both only on registration_required crossings. |
| `data.borders[].at_km` | Where it falls on the route, with at_drive_seconds and distance_from_route_km. |
| `data.borders[].alternatives[]` | Other crossings worth switching to: ppid, name, wait_minutes, queue_cars, detour_km. |
| `data.stops[].type` | break, meal, fuel, overnight, weekly_rest (45 h, eu561), border_wait or ban_wait, with reason, at_km, at_drive_s, minutes, starts_at and ends_at. |
| `data.stops[].counts_as` | On a border_wait or ban_wait stop: what the wait satisfies — none, break, meal, overnight or weekly_rest — so it is not planned twice. |
| `data.stops[].ban` | On a ban_wait stop: the driving ban being waited out — name, country, from, until. |
| `data.stops[].places[]` | With stop_places=1, on break, meal, overnight and weekly_rest stops: candidate parkings inside the pause window, best first — name, type (truck_stop, autohof, rest_area, parking), country, lat, lon, at_km, at_drive_s, offset_min (minutes from the planned stop, negative = before it), off_route_km, detour_km (same value), exceeds_4h30 (continuous driving since the last break would pass 4 h 30), type_rank (1 truck_stop … 4 parking). Tune with places_before_min, places_after_min, places_radius_km, rest_radius_km. |
| `data.stops[].in_vehicle_allowed` | v3, on daily and weekly rest stops: false for a regular weekly rest (45 h or more) and for any weekly rest of more than 45 h; true for a reduced weekly rest (24 h to less than 45 h) and for daily rests. accommodation_required has the opposite value. rule = EU 561/2006 Art. 8(8) as amended by 2020/1054. |
| `data.stops[].accommodation[]` | v3, on weekly rest stops: parkings with rooms on site near the stop (route-plan: the driving window of places[]; truck-route: 45 min of driving before to 15 min after) — name, type, country, lat, lon, at_km, offset_min, off_route_km, rooms_on_site. Information only: the stop and its truck parking do not change. |
| `data.stops[].place.has_accommodation` | v3, on weekly rest stops: true when this parking (in route-plan also each places[] row) has rooms on site, i.e. a site with rooms within 300 m. nearest_accommodation_km = distance in km to the nearest accommodation[] site, or null. |
| `data.stops[].warning` | v3: no_accommodation_found when accommodation_required is true and accommodation[] is empty. Information only; the stop stays in the plan. |
| `data.summary` | The timeline totals: drive_seconds, stop_seconds, border_wait_seconds, total_seconds, arrival_at, stop_count, border_count and borders_absorbed_breaks — long waits that satisfied a due break instead of adding to it. |
| `data.model` | Rules the plan was built on: vehicle, basis (driving-hygiene or EU 561/2006), break_after_drive_min, meal_after_drive_min, fuel_every_km. |
| `data.warnings[]` | Things to tell the driver: code, ppid, name, status, message, suggested_alternative — e.g. checkpoint_closed, wait_capped (a queue over 48 h is planned at 48 h; the eCherha wait is not capped), radius_clamped (places_radius_km or rest_radius_km above 25 km; with param, requested, used), echerha_registration (registration needed, with registration_wait_minutes), echerha_wait_unverified (eCherha wait missing or older than 3 h; check https://echerha.gov.ua). |
| `api_version` | v2 is additive only: the places[], ban_wait, weekly_rest, counts_as, registration_* and driver-state parameters above are opt-in or extra fields; rest_rules defaults to legacy and place names use the original filter. v3 (/api/v3/data/route-plan) changes defaults: rest_rules=eu561, strict place names (real parkings and rest areas only), the eCherha published wait (uncapped) used as the border wait, and a border wait counts as the break or rest that was due. Pass rest_rules=legacy to keep the old model on v3. |


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
*Auto-generated 2026-10-24 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
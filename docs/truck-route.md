# Truck Route API

Truck routing API with fuel stations and truck parking along the route, plus weigh stations and a driver rest plan. Send origin, destination and your vehicle (height, weight, length, width, axle load, hazmat) and get an HGV-legal route with the restrictions and dated truck driving bans it meets, the border crossings on it with their live wait, priced fuel stations, truck parkings (secure flag, capacity, amenities) and weighbridges within a corridor of the line. With include=rest the EU 561/2006 rules are applied to your driver state: 45-minute breaks at most 4 h 30 apart, daily rests before the 9 h / 10 h limit, each placed at a real parking before the limit is reached, weekend and holiday bans waited out, border waits on the timeline, single or double crew — and the ETA includes all of it. The rest plan is a planning aid, not a tachograph record.

**Endpoint:** `GET /api/v2/data/truck-route`
**Quota class:** route — 10/day on every account, more on paid plans, plus the Extra truck-route calls add-on (separate from the standard and heavy quotas)

---

## Parameters

| Name | Description |
|------|-------------|
| `from` | Origin as lat,lon (required unless from_city is given), e.g. 52.2297,21.0122 |
| `to` | Destination as lat,lon (required unless to_city is given) |
| `from_city` | Origin city name instead of coordinates (add from_country for accuracy) |
| `from_country` | ISO 3166-1 alpha-2 country of from_city, e.g. PL |
| `to_city` | Destination city name instead of coordinates |
| `to_country` | ISO 3166-1 alpha-2 country of to_city |
| `via` | Up to 5 waypoints as lat,lon\|lat,lon |
| `height` | Vehicle height in metres (1.0–5.0) |
| `weight` | Gross vehicle weight in tonnes (1–60); drives weight-limited restrictions and bans |
| `length` | Vehicle length in metres (2–30) |
| `width` | Vehicle width in metres (1.0–3.5) |
| `axle_load` | Axle load in tonnes (1–20) |
| `axles` | Number of axles, tractor plus trailer (2–12); used for routing and for toll classes (default for tolls: 5 from 18 t, 3 from 12 t, else 2) |
| `trailers` | Number of trailers (0–3); reported back, no routing effect yet |
| `hazmat` | Dangerous goods: 0 (default), 1 = ADR load with class not given, or a comma list of ADR classes 1–9 and/or explosive, gas, flammable, flammable_solid, oxidizing, poison, radioactive, corrosive, harmful_to_water |
| `adr_tunnel` | ADR tunnel restriction code of the load: B, C, D or E. Tunnels whose category forbids it are avoided (implies hazmat) |
| `avoid` | Comma list of tolls, ferries, motorways, borders, tunnels, unpaved, low_emission_zones |
| `emission_class` | Vehicle emission class euro0 … euro6; used for low-emission-zone checks and toll notes (tolls are priced at Euro VI rates) |
| `alternatives` | Number of alternative routes, 0–2 (default 0; routes without via points only) |
| `geometry` | polyline5 (default), polyline6 or geojson |
| `arrive_at` | Wanted arrival as RFC 3339; the departure is planned backwards including breaks and rests (instead of depart_at) |
| `route_type` | fastest (default) or eco |
| `include` | Comma list of fuel, parking, weigh, rest (default: all four) |
| `corridor_km` | Max distance from the route for fuel, parking and weigh stations, 0.2–5 km (default 2) |
| `fuel` | Fuel grades to price, comma list (default diesel) |
| `depart_at` | Departure as ISO 8601 with time zone, up to 14 days ahead (default: now) |
| `aliases` | Accepted for easy switching: depart → depart_at, departAt, arriveAt, vehicleHeight, vehicleWidth, vehicleLength, vehicleWeight (kg or t), vehicleAxleWeight / axleWeight → axle_load, vehicleNumberOfAxles / axleCount → axles, vehicleTrailers → trailers, vehicleAdrTunnelRestrictionCode → adr_tunnel, vehicleEmissionClass → emission_class, maxAlternatives → alternatives, waypoints → via |
| `crew` | 1 (default) or 2 drivers — double crew uses the 30-hour daily rest window and 9 h rests |
| `driven_since_break_min` | Driver state: minutes driven since the last 45-minute break (default 0) |
| `driven_today_min` | Driver state: minutes driven since the last daily rest (default 0) |
| `driven_week_min` | Driver state: minutes driven this calendar week (default 0) |
| `driven_last_week_min` | Driver state: minutes driven last week, for the 90 h fortnight limit (default 0) |
| `reduced_daily_rests_used` | Reduced (9 h) daily rests already taken since the last weekly rest, 0–3 (default 0) |
| `extended_days_used` | 10-hour driving days already used this week, 0–2 (default 0) |
| `last_weekly_rest_end` | ISO 8601 end of the last weekly rest (default: assumed just before departure) |
| `reduced_weekly_ok` | 1 = a reduced (24 h) weekly rest may be planned (default 0 = regular 45 h) |
| `lang` | Language code for names and messages (default en) |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/truck-route?from=52.2297,21.0122&to=52.52,13.405&weight=40&height=4&axles=5&emission_class=euro6&crew=1" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.route` | distance_m, duration_s (pure driving), geometry (format in geometry_format), bbox, countries (ISO codes in order), departure_at, toll_distance_m, fuel_l, ascent_m, descent_m, legal, avoid; with arrive_at also arrive_at_requested. |
| `data.route.country_split[]` | Per country in route order: country, km, duration_min. |
| `data.route.adr` | Only with adr_tunnel: tunnel_code, tunnels_avoided[] and tunnels_on_route[] (name, category, lat, lon). |
| `data.route.low_emission_zones_avoided[]` | Only with avoid=low_emission_zones: zones the route was moved out of (name, country); low_emission_zones_on_route[] lists any that could not be avoided. |
| `data.tolls` | Estimated truck tolls: currency (EUR), total_eur, complete, by_country[] (country, km_toll, cost_eur, basis), objects[] (priced tunnels and bridges: name, type, country, lat, lon, cost_eur, basis), estimate, coverage_note. A country we cannot price has cost_eur null and is named in coverage_note. |
| `data.alternatives[]` | Only with alternatives: distance_m, duration_s, tolls (currency, total_eur, complete, by_country) and geometry for each alternative route. Alternatives are planned without time-dependent restrictions and are not ban-checked; the main route is. Empty when no reasonable alternative exists. |
| `data.route.restriction_hits[]` | Restrictions for this vehicle still on the line: kind, name, country, at_km. |
| `data.route.truck_bans[]` | Dated truck driving bans on the route: name, kind, country, details, min_weight_t, window_from, window_to, eta_at_ban, km_from_start, applies, active, avoided. |
| `data.route.border_crossings[]` | Crossings on the route: name, ppid, from_country, to_country, lat, lon, km_from_start, status; wait_min = time on the exit side + the entry side at the planned arrival (wait_at), null when neither side is measured (wait_minutes = same value, legacy name); wait_sides[] (side country, role exit\|entry, ppid, min, basis live\|forecast, note when not measured); wait_notes[]; wait_capped (a side over 48 h is planned at 48 h); registration_required = an electronic-queue lane: register before arriving, the time to get a slot is not included in the wait. |
| `data.route.warnings[]` | code and message, e.g. ban_avoidance_skipped, restrictions_on_route, adr_tunnel_on_route, adr_tunnel_not_checked, low_emission_zone_on_route, tolls_not_fully_avoided, arrive_at_not_reachable. |
| `data.rest_plan[]` | EU 561/2006 stops in order: type (break, daily_rest, weekly_rest), start_at, end_at, duration_min, km_from_start, parking (a parkings[] row or null), reason, overlaps_ban, counts_as. |
| `data.waits[]` | Forced waits on the timeline: ban_wait (a driving ban) and border_wait, same shape as rest_plan. |
| `data.eta` | Arrival time including driving, breaks, rests, ban waits and border waits; with driving_time_min, total_time_min and crew. |
| `data.weekly_rest_check` | next_weekly_rest_due_by, weekly_rest_on_trip, driven_week_min_at_arrival, driven_fortnight_min_at_arrival, weekly and fortnight limits, reduced rests and 10 h days used at arrival. |
| `data.assumptions[]` | Defaults applied to driver state you did not send, and country rules not planned for. |
| `data.rest_warnings[]` | code, km_from_start, message — e.g. no_parking_in_window when a stop had to be placed at the legal limit. |
| `data.fuel_stations[]` | name, brand, lat, lon, country, km_from_start, distance_from_route_km, detour_km, prices[] (fuel_type, price, currency, stale), price_updated_at. |
| `data.parkings[]` | name, type (parking, rest_area, truck_stop, autohof), lat, lon, country, km_from_start, distance_from_route_km, detour_km, capacity, secure, amenities[], opening_hours. |
| `data.weigh_stations[]` | name, lat, lon, country, km_from_start, distance_from_route_km, detour_km, type and side (null when not known). |
| `data.coverage` | corridor_km, countries_on_route, fuel_grades, fuel countries with sparse or no price data, truncated lists, measured_at. |
| `data.resolved_locations` | Only with from_city/to_city: query, lat, lon, label of the geocoded point. |
| `data.disclaimer` | The rest plan is a planning aid, not a tachograph record or legal advice. |
| `data.attribution` | List of credits to display: always "Data by nakordoni.eu"; plus "Weigh stations: © OpenStreetMap contributors (ODbL)" whenever weigh_stations is non-empty. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "truck-route",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#truck-route
*Auto-generated 2026-10-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Bus Sun Side API

Which side of a coach gets the sun or the shade on a trip, and which seats are best for shade or for the view. The trip is walked in 5-minute steps: position and heading from the route, solar position at that place and time, then a per-seat estimate for the chosen bus layout. Use a route from our licensed-route registry (route_id), two points (from/to), or POST your own route (stops or a polyline) to also get a seat map, a route map and a timeline as images. Results are an estimate from geometry and solar position; real shade also depends on curtains, buildings, terrain and weather.

**Endpoint:** `GET /api/v2/data/bus-sun-side`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `route_id` | Route id from our bus route registry (the same ids as the bus pages on nakordoni.eu). Use route_id or from+to. |
| `dir` | direct (default) or return. |
| `date` | Travel date YYYY-MM-DD (default today). The sun position depends on the date. |
| `dep` | Departure time HH:MM local to the route (default: the registry timetable). |
| `from` | Free form: start point lat,lon (with to). |
| `to` | Free form: end point lat,lon (with from). |
| `depart` | Free form: departure YYYY-MM-DDTHH:MM, local time of the start point. |
| `lang` | Language of the place names and texts, 25 languages (en, uk, pl, de, …). Default en. |
| `bus_model` | Bus model id from bus-sun-catalog (default standard_coach). Any field below overrides the model. |
| `rows` | Seat rows, 8-20. |
| `layout` | Seat layout: 2+2, 2+1 or 1+2 (left+right of the aisle). |
| `door_side` | right (continental Europe) or left. |
| `windshield` | large or normal — how much sun the first rows get through the windscreen. |
| `rear_window` | none, small or large — sun on the last rows. |
| `window_height` | standard or panoramic (tall side windows). |
| `deck` | single, upper or lower (double-deck coaches). |
| `mid_door` | 1 or 0 — middle door present (removes the seats there). |
| `timeline` | 0 = leave out the timeline (15-minute rows). |
| `track` | 0 = leave out the timed track points. |
| `weather` | 1 (default) or 0. With 1, a weather forecast along the trip is used when the trip is within the next 3 days (72 h); later trips and weather=0 use clear sky. |
| `animation` | 1 (default) or 0. With 0, the animation block (sun and coach position every 5 minutes) is left out. POST body: animation true\|false. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/bus-sun-side?route_id=521&dir=direct&date=2026-06-21&bus_model=standard_coach&lang=en" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.date / data.tz` | Travel date and the time zone of the times in the answer. |
| `data.route` | id, num, from, to, dir, dep, arr, km (registry route) — or the stops you sent (POST). |
| `data.basis` | How the timed path was built: timetable (registry or your stop times), router (our road route between points) or geometry (your polyline). |
| `data.bus` | The bus profile used: model, layout, rows, door_side, windshield, rear_window, window_height, deck, mid_door. |
| `data.summary` | trip_min, daylight_min, night_min and minutes with the sun on the left, right, ahead and behind (weather-weighted when a forecast was used). |
| `data.recommend` | shade_side, sun_side, shade_pct, best_shade_seats[], best_sun_seats[], confidence (high \| medium \| low), reason_key; tip_key hot_sun_side when the sunny side is forecast at 28 °C or more. reason_key mostly_cloudy (shade_side any) when the forecast is mostly cloud or rain. |
| `data.recommend_clear` | The same recommendation for a clear sky (no weather). |
| `data.weather_changes_answer` | true when the forecast changes the shade side compared with recommend_clear. |
| `data.weather` | used, window_h (72), covered_pct, sunny_min, cloudy_min, rain_min, max_temp, note_key (forecast_used \| partly_forecast \| mostly_cloudy \| rain_expected \| clear_sky_only). |
| `data.zones` | front / middle / back: left and right sun exposure 0-100 and rows [first, last] of that part. |
| `data.seats` | layout, rows, cols[], grid[][]: every seat — id (e.g. 4C), side, pos (window \| aisle), exp (sun exposure 0-100), door (true next to a door). |
| `data.timeline[]` | One row per 15 min: t, lat, lon, hd (heading), az / el (sun azimuth / elevation), side (left \| right \| ahead \| behind \| none), w (sun strength); wx {code, f, temp, precip} when a forecast was used. |
| `data.track[]` | Track points [lat, lon, sun side] for drawing the route (leave out with track=0). |
| `data.input` | POST only: how your route was read — source, timed_by, stops, points, legs_routed, legs_straight, timezone. |
| `data.images` | POST only: seat_map, route_map, timeline — each {url, format, expires_at}; url = https://api.nakordoni.dev/images/<random name>.<png \| webp \| jpg> (body format, default png). Files are kept 12 hours. format svg: PNG files plus the seat map as inline SVG (seat_map.svg). |
| `data.embed_html` | POST only: a self-contained HTML figure with the seat map (inline SVG) to paste into a page; null with embed_html=false. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "bus-sun-side",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#bus-sun-side
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
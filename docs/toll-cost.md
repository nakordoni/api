# Truck Toll Cost API

Truck toll cost for a route across Europe. Send the route line as coordinates (for example the geometry from truck-route or from your own router), or just waypoints and we route them with our truck routing. Set the vehicle (axles, gross weight, Euro emission class, CO2 emission class) and get the total in EUR or your currency, a per-country breakdown (tolled km, effective EUR per km, cost, the vehicle class priced, whether a class fallback was used, date of the tariff), motorway concession sections, tunnels and bridges with their own truck tolls (for example Calafat-Vidin) as separate items, and the tolled km in total. Per-km HGV tolls and HGV vignettes (DE, AT, PL, CZ, SK, HU, BG, RO, CH, BE, NL, DK, SE, SI and more) are priced from official tariff tables. Euro and CO2 class specific rates are used where an official tariff for that class is on file; otherwise the default class rate is used and class_fallback is true. In France and Spain, a motorway stretch between two toll stations on the route is priced from the entry/exit tariff of the motorway operator for the vehicle class (France: class 3 for 2 axles, class 4 for 3 or more axles; Spain: Pesados 1 for 2 or 3 axles, Pesados 2 for 4 or more axles) and shown as a motorway_section item; the other tolled km of the country use the per-km average. The French, Italian and Spanish tariffs do not depend on the Euro or CO2 class, so class_fallback is false there. Estimates are net of VAT.

**Endpoint:** `GET /api/v2/data/toll-cost`
**Quota class:** route — 10/day on every account, more on paid plans, plus the Extra truck-route calls add-on (separate from the standard and heavy quotas)

---

## Parameters

| Name | Description |
|------|-------------|
| `coordinates` | (JSON body, this or waypoints) The route line as [[lon, lat], ...] — GeoJSON order, points at most ~1 km apart (a decoded route geometry), up to 50,000 points and 6000 km. It is matched to our road graph and priced as sent. |
| `waypoints` | (JSON body, this or coordinates) 2 to 7 points as [lon, lat] or {lat, lon}: origin, up to 5 via points, destination. We compute the fastest truck route for this vehicle and price it. Dated driving bans do not change this route; for a ban-aware plan call truck-route and send its geometry as coordinates. |
| `vehicle` | (JSON body) {axles: 2-12 (default 5), gross_weight_t: 3.5-60 (default 40), euro: 0-6 (default 6), co2_class: 1-5 (default none)}. |
| `date` | (JSON body) Travel date YYYY-MM-DD within one year of today (default today). Tariffs valid on that date are used; with waypoints, the route is computed for that day. |
| `currency` | (JSON body) ISO 4217 code for total (default EUR), e.g. PLN, CZK, HUF, RON, BGN, CHF, SEK, DKK. All line items stay in EUR. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/toll-cost" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.total_eur` | Total toll cost in EUR (net of VAT): per-country tolls plus every priced item. |
| `data.total` | The total in currency (fx_eur_to_currency is the rate used; null for EUR). |
| `data.complete` | false when a country or item on the line could not be priced (named in not_priced / items with cost_eur null) — then the total is a lower bound. |
| `data.toll_km` | Tolled kilometres in total (countries plus concession sections). |
| `data.distance_km` | Length of the priced line. |
| `data.by_country[]` | cc, toll_km, scheme (per_km \| toll_road \| vignette \| not_tolled), rate_eur_km (effective EUR per tolled km; null for vignettes), cost_eur, vehicle_class (tariff class priced), class_fallback (true = no tariff for the asked Euro / CO2 class, the default class rate was used), source_date (date the tariff is valid from), period (vignettes), class_note (for example: tariff not class-dependent). A country on the line with no tolled road gets cost_eur 0 and scheme not_tolled. |
| `data.items[]` | Separately charged parts: type (concession \| motorway_section \| bridge \| tunnel), name, cc, cost_eur, basis; concessions and motorway sections add toll_km (a motorway section is not counted again in its country row), crossings add lat / lon. motorway_section name = operator or road and the entry and exit stations. A cost_eur of 0 means no separate toll (included in the national charge). |
| `data.not_priced[]` | Countries with tolled kilometres but no truck rate on file yet. |
| `data.class_fallback` | true when any country used a fallback class. |
| `data.vehicle` | The vehicle priced (defaults filled in): axles, gross_weight_t, euro, co2_class. |
| `data.date` | Tariff date used. input is coordinates or waypoints. |
| `data.disclaimers[]` | What the estimate covers and what it does not. |
| `error.code` | bad_request (send exactly one of coordinates or waypoints), bad_coordinates, coordinates_too_sparse (two points more than 1.5 km apart), coordinates_not_matched, bad_waypoints (2 to 7 points), bad_vehicle, bad_date, bad_currency, payload_too_large (2 MB); with reason no_route (no truck route between the waypoints for this vehicle on this date), route_too_long (6000 km), route_refused / occupied_ua (line into Russia, Kaliningrad or the occupied territory of Ukraine). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "toll-cost",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#toll-cost
*Auto-generated 2026-10-28 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
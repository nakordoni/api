# Lane Cost Index API

Cost index of a truck lane in Europe: the EUR per km cost floor of one loaded trip from A to B for a vehicle class — fuel, tolls, driver time and per diem. Per country on the route you get the km, the toll (and the toll vehicle class priced), the diesel litres and cost. Driver time follows the EU 561/2006 driving and rest rules. A 3-month trend shows how the cost index moved. It is a cost index, not a freight price: vehicle capital, insurance, overhead and margin are not included. Fuel and tolls are net of VAT. When a country on the route has no toll or fuel data, or the driver rate of the origin country is not on file, incomplete is true and the total is a lower bound.

**Endpoint:** `GET /api/v2/data/lane-cost-index`
**Quota class:** route — 10/day on every account, more on paid plans, plus the Extra truck-route calls add-on (separate from the standard and heavy quotas)

---

## Parameters

| Name | Description |
|------|-------------|
| `from` | Origin: "lat,lng" (for example 52.4064,16.9252) or a city id (poznan, warsaw, berlin, munich, bucharest, zaragoza, lyon, milan, vienna, sofia, budapest). The carrier is assumed to be based in the origin country. |
| `to` | Destination: "lat,lng" or a city id. |
| `vehicle` | Vehicle preset (default artic_40t): van_3_5t, rigid_7_5t, rigid_12t, rigid_18t, artic_26t, artic_40t, artic_44t. |
| `euro` | Euro emission class 0-6 (default 6), used for the tolls. |
| `co2_class` | CO2 emission class 1-5 (default 1), used for the tolls. |
| `empty_return` | 1 = add the empty return trip on the same route (fuel, tolls, driver time); eur_per_km stays per loaded km. Default 0. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/lane-cost-index?from=poznan&to=berlin&vehicle=artic_40t" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.label` | Always "cost index". |
| `data.lane` | key, from {cc, name}, to {cc, name}. |
| `data.vehicle` | key, gross_weight_t, axles, euro, co2_class. |
| `data.km_total` | Route length in km (one way). km_driven includes the empty return when asked. |
| `data.per_country[]` | cc, km, toll_eur, fuel_eur, fuel_l, diesel_eur_l (net of VAT), toll_vehicle_class. |
| `data.toll_eur` | Tolls in EUR (net of VAT). |
| `data.fuel_eur` | Fuel in EUR (net of VAT); fuel_l = litres. |
| `data.driver_eur` | Driver cost for the paid hours (null when the origin country rate is not on file). |
| `data.per_diem_eur` | Per diem (null when not on file for the origin country). |
| `data.total_eur` | Sum of the components; eur_per_km = total_eur / km_total. |
| `data.incomplete` | true when missing_countries (no toll or fuel data) or missing_components is not empty — total_eur is then a lower bound. |
| `data.time` | driving_h, break_h, rest_h, load_unload_h, duty_h (paid), elapsed_h, daily_rests, weekly_rests. |
| `data.assumptions` | consumption (l/100 km), fuel_price_basis, driver_rate (EUR/h), driver_country, paid_hours, per_diem_scheme, prices_date, toll_date, excludes. |
| `data.components_source_dates` | Dates of the data used: fuel {from, to}, toll (tariff valid from), driver (dataset), per_diem_fx. |
| `data.trend` | months[] {month, eur_per_km, total_eur, incomplete} for the current and the two previous months. |
| `data.flags[]` | Machine-readable notes, for example toll_not_priced:RS. |
| `data.disclaimers[]` | What the cost index covers and what it does not. |
| `error.code` | missing_from, missing_to, bad_from, bad_to, unknown_city, bad_vehicle, bad_euro, bad_co2_class, bad_empty_return; with reason no_route, route_too_long, route_refused / occupied_ua (route into Russia, Kaliningrad or the occupied territory of Ukraine). |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "lane-cost-index",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#lane-cost-index
*Auto-generated 2026-10-26 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
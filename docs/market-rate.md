# Market Rate API

Market rate of a truck lane in Europe: the median and the 75th percentile price in EUR per km per month, from prices that transport companies agreed with their customers and chose to share. Anonymised: a month is published only when at least 5 different companies contributed on that lane and vehicle class; otherwise the month is insufficient_data and no number is given. No company and no single price is ever shown. Use it with the Lane Cost Index API: the cost index is the cost floor, the market rate is what the market paid.

**Endpoint:** `GET /api/v2/data/market-rate`
**Quota class:** cheap — 1000/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `from` | Origin: a country code (PL), "lat,lng" (for example 52.4064,16.9252) or a city id (poznan, warsaw, berlin, munich, bucharest, zaragoza, lyon, milan, vienna, sofia, budapest). |
| `to` | Destination: a country code, "lat,lng" or a city id. |
| `vehicle` | Vehicle class (default artic): van, rigid, artic. Lane Cost Index presets (van_3_5t, rigid_18t, artic_40t, ...) are accepted. |
| `level` | country (default) = all trips between the two countries; region = trips between the 2 x 3 degree grid cells of the given points (needs "lat,lng" or a city id on both ends). |
| `months` | Number of months, 1-12 (default 6). |
| `until` | Last month YYYY-MM (default the current month), within the last 24 months. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/market-rate?from=PL&to=DE&vehicle=artic" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.label` | Always "market rate". |
| `data.lane` | level, from {cc, region, region_label}, to {cc, region, region_label}; region is null with level=country. |
| `data.vehicle_class` | van, rigid or artic. |
| `data.currency` | Always EUR; data.unit = eur_per_km. |
| `data.min_companies` | Minimum number of different companies for a month to be published (5). |
| `data.months[]` | month (YYYY-MM), status ok \| insufficient_data; with ok: median_eur_km, p75_eur_km, n_companies. |
| `data.published_months` | Number of months with status ok. |
| `data.disclaimers[]` | What the market rate covers. |
| `error.code` | missing_from, missing_to, bad_from, bad_to, unknown_city, lane_refused (Russia, Kaliningrad, Belarus), bad_vehicle, bad_level, bad_months, bad_until. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "market-rate",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#market-rate
*Auto-generated 2026-10-28 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
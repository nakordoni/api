# Nakordoni Developer API

> Real-time border queue data for Europe — live queues, ML forecasts, fuel prices, POIs and more.

**Portal:** https://nakordoni.eu/en/developers
**Docs:** https://nakordoni.eu/en/developers/docs
**Status:** https://nakordoni.eu/en/status
**Changelog:** https://nakordoni.eu/en/developers/changelog
**Raw API reference (for AI assistants):** https://nakordoni.eu/api/v1/docs.md

---

## Quick Start

```bash
curl "https://nakordoni.eu/api/v1/data/queue?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

[Get a free Explorer key →](https://nakordoni.eu/en/developers/signup) (email only, instant, no card)

---

## Products

| Slug | Name | Description | Quota class |
|------|------|-------------|-------------|
| `status` | API Status | Live health of every Developer-API product: online / degraded / offline, response latency and last-checked time, plus an overall rollup. Public — no API key required, never counts against your quota. Refreshed every 5 minutes. Mirrors the human status page at nakordoni.eu/{lang}/status. | standard |
| `checkpoints` | Checkpoints Directory API | Directory of all monitored border checkpoints: IDs, names, countries, coordinates and status. Use it to discover ppid values for the other APIs. | standard |
| `multi` | Multi-Checkpoint API | Fetch live queue status and/or data freshness for up to 20 checkpoints in a single request. Designed for dashboard builders who poll many PPIDs simultaneously — reduces 20+ individual calls to one. Quota counts as ⌈(N PPIDs × sub-products) / 2⌉ — 50% cheaper than the equivalent individual calls. Use include=queue,update-info to get both datasets at once. | standard |
| `border` | Border Queue API | All checkpoints on a given border + vehicle type in one call — live queue, wait estimate, and data freshness for every crossing point. Supports single destination, comma-separated list, or "all" to query all neighbours at once. Results sorted by queue length ascending, each checkpoint tagged with its border country. | standard |
| `search` | Checkpoint Search API | Find checkpoint PPIDs by name. Pass a single name or a comma-separated list (up to 20). Searches all translation languages; returns all PPIDs at that location grouped by vehicle type (4=car, 5=taxfree car, 6=bus, 7=pedestrian, 8=truck<7.5t, 9=truck). Use this to quickly discover the right ppid before calling the queue or forecast APIs. | standard |
| `queue` | Live Border Queue API | Real-time queue length, wait estimate and status for any monitored checkpoint, plus hourly/daily aggregates. | standard |
| `stats` | Checkpoint Hourly Statistics API | Hourly historical queue stats per checkpoint and date: 24 hourly values, daily avg/min/max, peak and quietest hours, day-over-day comparison. | standard |
| `day-stats` | Best Time to Cross API | Typical-week load statistics per checkpoint: 7×24 day-of-week × hour matrix (median + p25/p75 band), quietest/busiest day, best/worst 2-hour windows. Precomputed daily from ~60 days of real observations. | standard |
| `forecast` | Queue Forecast API | ML ensemble forecast of queue levels: 24-hour and 7-day (168h) horizons with confidence bounds. The same model that powers nakordoni.eu predictions. | standard |
| `alternatives` | Checkpoint Alternatives API | Nearby alternative checkpoints on the same border with current queues and distance deltas. By default filters to the same vehicle type as the requested ppid. Use crossing_type to override — e.g. crossing_type=4 for cars, crossing_type=7 for pedestrians. | standard |
| `update-info` | Data Freshness API | When a checkpoint was last updated, by which source, and a freshness rating. | standard |
| `fuel` | EU Fuel Prices API | Fuel prices across EU countries — country averages, nearest stations by coordinates, or stations near a border checkpoint. Aggregated from official national sources (tankerkoenig for DE, petrol.pl for PL, fuelo.net for HU/SK/RO, EU bulletin for others). | standard |
| `fuel-cities` | Fuel Prices by City API | Per-city fuel price summary for a country: cheapest station price and average across the top 5 stations in each major city. Covers the same countries as the nakordoni.eu fuel pages (AT, DE, FR, ES, IT, PT, SI, LU, RO, DK, HR). | standard |
| `pois` | Driver POIs API | Truck parkings (14k+), free showers, services and supermarkets across Europe with coordinates. | standard |
| `truck-bans` | Truck Driving Bans API | European truck driving restrictions by country and date, including seasonal and holiday bans. | standard |
| `trading-sundays` | Trading Sundays API | Sunday retail-opening regulations and upcoming trading Sundays per regulated EU country. | standard |
| `bus-carriers` | Bus Carrier Border Stats API | Border-crossing performance per bus carrier: crossings, average/median/min/max wait minutes — built from our own plate-matched crossing records. | standard |
| `road-conditions` | Road Conditions API | Approved road condition reports near borders and on major corridors: potholes, roadworks, closures, ice, hazards — combining driver reports with automatic accelerometer detections from our navigation app. | standard |
| `assistant` | Border AI Assistant API | Ask our production AI assistant any border-crossing question (queues, forecasts, rules, fuel, routes) and get the same grounded answer that powers the nakordoni.eu widget — in 24 languages. Already used in production by yaknakordoni.com.ua. | standard |
| `travel-matrix` | Travel Matrix API | Travel time + border queue data for all checkpoints from a given origin. Returns drive time, current queue, total estimated journey time, and distance for every relevant crossing — sorted from fastest. Supports multi-leg routes (e.g. Germany → Poland → Ukraine border). Powers the nakordoni.eu navigator "Choose a crossing" feature. | standard |


**Quota classes** (Explorer free tier): `standard` = 200 req/day · `heavy` = 50 req/day

---

## History data export

Approved developers can download **hourly-averaged, published** border-queue history for up to **5 checkpoints** (rolling window up to **90 days**) as gzipped **CSV** or **NDJSON**. This is **portal-only** — it is *not* an API endpoint; you build and download exports from the **Data export** tab in your account.

- **Access** is granted on request — open a [Data ticket](https://nakordoni.eu/en/developers/tickets?cat=data) with the checkpoints, time window and intended use. Default limit: 1 export/day, ≤5 checkpoints.
- **Fields** (one row per checkpoint per UTC hour): `ppid, checkpoint_name, hour_utc, direction, vehicle_type, avg_queue_length, avg_wait_minutes, sample_count, source`. `avg_wait_minutes` is `null` where a checkpoint has no official wait feed.
- **Quality:** published-only data, passed through our anomaly / data-quality checks (no raw per-report data). Files kept 10 days.
- **Provenance:** every file embeds a signed fingerprint (`sha256` + `HMAC`) in its header, so any copy can be confirmed as genuine nakordoni.eu data and checked for tampering — even after download.

Full docs → [`docs/export.md`](docs/export.md) · https://nakordoni.eu/en/developers/docs#export

---

## Authentication

Every request must include:

```
Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX
```

The key is issued instantly after email verification. No credit card required.

---

## Response Envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ],
  "snapshot": { ... },
  "usage": { "requests_today": 12, "limit_today": 200, "remaining": 188 }
}
```

Errors return `ok: false` with `error.code` and the appropriate HTTP status.

---

## Code Samples

**JavaScript**
```js
const res = await fetch('https://nakordoni.eu/api/v1/data/queue?ppid=id_13', {
  headers: { 'Authorization': 'Bearer NKD-DEV-XXXX-XXXX-XXXX' }
});
const { data, snapshot } = await res.json();
console.log('Queue:', snapshot.queue_now, 'cars | Wait:', snapshot.wait_min, 'min');
```

**Python**
```python
import os, requests
KEY = os.environ["NKD_DEV_KEY"]
r = requests.get("https://nakordoni.eu/api/v1/data/queue",
                 params={"ppid": "id_13"},
                 headers={"Authorization": f"Bearer {KEY}"})
snap = r.json()["snapshot"]
print(f"Queue: {snap['queue_now']} cars | Wait: {snap['wait_min']} min")
```

**PHP**
```php
$ch = curl_init("https://nakordoni.eu/api/v1/data/queue?ppid=id_13");
curl_setopt($ch, CURLOPT_HTTPHEADER, ["Authorization: Bearer " . getenv("NKD_DEV_KEY")]);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$r = json_decode(curl_exec($ch), true);
echo $r["snapshot"]["queue_now"] . " cars in queue";
```

---

## Reference

### Country codes
`1`=UA `2`=PL `3`=SK `4`=HU `5`=RO `6`=MD `7`=BY `8`=LT `9`=LV
`10`=EE `11`=SI `12`=BG `13`=RS `14`=TR `18`=DE `19`=GR `21`=AL `23`=XK

### Vehicle / crossing types
`4`=Car UA→EU · `5`=Car EU→UA · `6`=Bus · `7`=Pedestrian · `8`=Truck <7.5t · `9`=Truck

### PPID format
Checkpoints are identified by `id_NNN` (e.g. `id_13` = Rava-Ruska UA/PL car crossing).
Use the [`checkpoints`](docs/checkpoints.md) or [`search`](docs/search.md) product to discover PPIDs.

---

## Attribution

Explorer-plan integrations must display a visible "Data by nakordoni.eu" link on the page where data appears:

```html
<a href="https://nakordoni.eu" rel="noopener">Data by nakordoni.eu</a>
```

Pay As You Grow customers may omit attribution.

---

## Per-product docs

See the [`docs/`](docs/) directory for one file per product with endpoint details, parameters and example responses.

OpenAPI 3.0 spec: [`openapi.yaml`](openapi.yaml)

---

*Last updated: 2026-07-03*
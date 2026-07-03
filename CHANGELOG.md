# Changelog

All notable changes to the Nakordoni Developer API.

See also the live changelog: https://nakordoni.eu/en/developers/changelog

---

## 2026-07-03

### Added
- **History data export** — approved developers can download hourly-averaged, published border-queue history for up to **5 checkpoints** (rolling window up to **90 days**) as gzipped **CSV** or **NDJSON**, from the **Data export** tab in the portal. This is a **portal-only** feature — **not** an API endpoint. One row per checkpoint per UTC hour: `ppid, checkpoint_name, hour_utc, direction, vehicle_type, avg_queue_length, avg_wait_minutes, sample_count, source` (`avg_wait_minutes` is `null` where a checkpoint has no official wait feed). Published-only + data-quality filtered; files kept 10 days. Every file embeds a signed provenance fingerprint (`sha256` + `HMAC`) so any copy can be verified as genuine nakordoni.eu data. Access on request via a [Data ticket](https://nakordoni.eu/en/developers/tickets?cat=data). See [`docs/export.md`](docs/export.md).

## 2026-06-12

### Added
- **`queue` product** — `snapshot` block with prognosed wait time (`wait_min = tmin + queue_now × tpercar`), same formula as the nakordoni.eu hero section
- **`border` product** — query all checkpoints on a given border + vehicle type in one call
- **`search` product** — find checkpoint PPIDs by name in any of 24 languages
- **`alternatives` product** — `crossing_type` parameter to override vehicle type filter; `limit` parameter

### Improved
- Localized `crossing_type_label` and country names in `checkpoints`, `border`, and `search` responses — all 22 supported languages

---

## 2026-06-05

### Added
- Developer portal launched at https://nakordoni.eu/en/developers
- Initial products: `checkpoints`, `queue`, `stats`, `day-stats`, `forecast`, `alternatives`, `update_info`, `fuel`, `pois`, `truck_bans`, `trading_sundays`, `bus_carriers`, `road_conditions`, `assistant`

# Changelog

All notable changes to the Nakordoni Developer API.

See also the live changelog: https://nakordoni.eu/en/developers/changelog

---

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

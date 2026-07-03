# History data export

Approved developers can download **hourly-averaged, published** border-queue history
for up to **5 checkpoints** (rolling window up to **90 days**) as gzipped **CSV** or
**NDJSON**.

> This is a **portal-only** feature — it is **not** an API endpoint (nothing under
> `/api/v1/data/…`). You build and download exports from the **Data export** tab in
> your developer account.

## Access

Bulk historical data is granted on request. Open a
[Data ticket](https://nakordoni.eu/en/developers/tickets?cat=data) telling us:

- which checkpoints (PPIDs) you need,
- the time window, and
- what you'll use the data for.

Once approved, the **Data export** tab appears in your account. Default limit:
**1 export/day, up to 5 checkpoints** each — ask us to raise it.

## Output

One row per checkpoint per UTC hour, in either `csv.gz` or `ndjson.gz`:

| Field | Description |
|-------|-------------|
| `ppid` | Checkpoint id (`id_NNN`) |
| `checkpoint_name` | Checkpoint name |
| `hour_utc` | Hour bucket, ISO-8601 UTC |
| `direction` | e.g. `UA->PL` |
| `vehicle_type` | `car` / `bus` / `truck` / `pedestrian` |
| `avg_queue_length` | Hourly average queue length |
| `avg_wait_minutes` | Hourly average wait; `null` where a checkpoint has no official wait feed |
| `sample_count` | Number of observations in the hour |
| `source` | Underlying data feed(s) |

```
# sha256: 3f9c…   # signature: e87b…
ppid,checkpoint_name,hour_utc,direction,vehicle_type,avg_queue_length,avg_wait_minutes,sample_count,source
id_10,Hrushiv,2026-06-12T02:00:00Z,UA->PL,car,11.3,,4,line+granicaua
```

## Quality & retention

Exports contain **published-only** data that has passed our anomaly / data-quality
checks (no raw per-report data). All timestamps are UTC. Files are kept **10 days**.

## Provenance

Every file embeds a signed fingerprint (`sha256` of the data body + `HMAC`) in its
header. That lets us confirm any copy is **genuine nakordoni.eu data** and detect
tampering — even long after download.

---

Full docs: https://nakordoni.eu/en/developers/docs#export

*Last updated: 2026-07-03*
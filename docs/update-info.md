# Data Freshness API

When a checkpoint was last updated, by which source, and a freshness rating.

**Endpoint:** `GET /api/v1/data/update-info?ppid=id_13`

> Counts against **standard** daily quota.

## Parameters

| Parameter | Description |
|---|---|
| `ppid` | Checkpoint ID |
| `lang` | Language code |

## Example

```bash
curl "https://nakordoni.eu/api/v1/data/update-info?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "update-info",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

[← Back to README](../README.md) · [Full docs](https://nakordoni.eu/en/developers/docs#update-info)

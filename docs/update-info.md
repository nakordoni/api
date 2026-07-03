# Data Freshness API

When a checkpoint was last updated, by which source, and a freshness rating.

**Endpoint:** `GET /api/v1/data/update-info`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `ppid` | Checkpoint ID |
| `lang` | Language code |


## Example

```bash
curl "/api/v1/data/update-info?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
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

Full docs: https://nakordoni.eu/en/developers/docs#update-info
*Auto-generated 2026-07-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
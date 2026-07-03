# Checkpoints Directory API

Directory of all monitored border checkpoints: IDs, names, countries, coordinates and status. Use it to discover ppid values for the other APIs.

**Endpoint:** `GET /api/v1/data/checkpoints`
**Quota class:** standard — 200/day (Explorer), 50000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `country` | Filter by numeric country code (1=UA, 2=PL, 3=SK, 4=HU, 5=RO, 6=MD, 7=BY, 8=LT, 9=LV, …) |
| `lang` | Language for checkpoint names (default en) |


## Example

```bash
curl "/api/v1/data/checkpoints" \
  -H "Authorization: Bearer NKD-DEV-YOUR-KEY-HERE"
```

## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "checkpoints",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#checkpoints
*Auto-generated 2026-07-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
# Plate Image Recognition API

Read the licence plates in a photo of a vehicle. POST one image (JPEG, PNG, WebP, BMP or TIFF, max 8 MB) as a JSON body with image_url or image_base64, as a multipart/form-data upload in the field "image", or as the raw bytes with an image/* Content-Type. Every plate found comes back with the text, a 0-1 score, alternative readings, the plate box in pixels and the detection score. The engine is our own plate detector and reader, tuned on European trucks and buses. Images are processed in memory and never stored. image_url must be a public http(s) address; private and internal addresses, and redirects to them, are refused, and the download is stopped after 10 seconds.

**Endpoint:** `GET /api/v2/data/plate-recognition`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `image_url` | (JSON body) Public http(s) URL of the image. Max 8 MB, max 3 redirects, 10 s download limit. |
| `image_base64` | (JSON body) The image as base64 (a data:image/...;base64, prefix is allowed). Send image_url or image_base64, not both. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/plate-recognition" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.success` | true when at least one plate was read; data.message says how many. |
| `data.processing_time` | Time spent on the request in milliseconds (download and recognition). |
| `data.results[]` | One entry per plate, highest detection score first. |
| `data.results[].plate` | Plate text, upper case, letters A-Z and digits only (no spaces or dashes). |
| `data.results[].score` | Reading confidence 0-1 (mean of the per-character confidences). |
| `data.results[].candidates[]` | Up to 5 readings {plate, score}, best first; the first one is the plate. |
| `data.results[].region` | {code, score} with the country of the plate when the engine can tell it; null otherwise (the current engine does not infer the country). |
| `data.results[].box` | Plate position in pixels: xmin, ymin, xmax, ymax. |
| `data.results[].dscore` | Plate detection confidence 0-1. |
| `data.results[].vehicle` | Vehicle details when available; null with the current engine. |
| `error.code` | 400: image_required, bad_json, bad_base64, one_image_only, bad_image_url, image_url_not_allowed, image_url_fetch_failed, image_url_timeout, invalid_image, unsupported_image_type, unsupported_content_type, upload_failed. 404: no_plate_detected. 413: payload_too_large. 500: engine_error. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "plate-recognition",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#plate-recognition
*Auto-generated 2026-10-27 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
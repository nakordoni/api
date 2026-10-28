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


## Version v4

v4 fills two fields that v2 always returns as null. results[].region gives the country of the plate as {code, score}, read from the plate format: the letter and digit pattern of each country and, where the format has one, the region or county prefix (for example the CE of a Ukrainian plate). results[].vehicle gives the vehicle that carries the plate as {type, score, box}, from our vehicle detector: type is car, bus, truck or motorcycle. v4 also accepts optional request options: regions, camera_id, timestamp, config.threshold and config.mode. Without them, the v4 output does not change. The other response fields and the error codes of v2 are the same. v2 keeps returning null for both fields and ignores the options.

**Endpoint:** `POST /api/v4/data/plate-recognition`

### Parameters (v4)

| Parameter | Description |
|-----------|-------------|
| `image_url` | (JSON body) Public http(s) URL of the image. Max 8 MB, max 3 redirects, 10 s download limit. |
| `image_base64` | (JSON body) The image as base64 (a data:image/...;base64, prefix is allowed). Send image_url or image_base64, not both. |
| `regions` | Optional. Countries you expect, as ISO 3166-1 alpha-2 codes: a JSON array ["PL","DE"] or a comma-separated string "PL,DE" (multipart field or query param). Up to 10. The region guess and the reading order are biased toward these countries: when the best reading matches no listed country format but another candidate that scores at most 0.15 lower does, that candidate becomes the plate. A plate that matches no listed country keeps its normal answer. |
| `camera_id` | Optional. Your camera or gate ID, up to 128 characters. Returned unchanged in data.camera_id. Not stored beyond the normal request log. |
| `timestamp` | Optional. The capture time in your own format (for example 2026-10-27T14:05:00Z), up to 64 characters. Returned unchanged in data.timestamp. Not stored beyond the normal request log. |
| `config` | Optional. JSON object {"threshold": 0-1, "mode": "fast"\|"full"}. In a multipart form or a query string, send it as a JSON string, or use the fields threshold and mode. |
| `threshold` | Optional, the same as config.threshold. Results with a score below this value (0-1) are left out. When none is left, the answer is 404 no_plate_detected. |
| `mode` | Optional, the same as config.mode. full (default) runs plate recognition and vehicle detection. fast skips vehicle detection for v2-like speed: region is still filled, vehicle is null. |

### Example (v4)

```bash
curl -X POST "https://nakordoni.eu/api/v4/data/plate-recognition" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"image_url":"https://nakordoni.eu/img/developers/plate-recognition-example-bus.jpg","regions":["UA"],"camera_id":"depot-gate-1","timestamp":"2026-10-27T14:05:00Z","config":{"threshold":0.8}}'
```

### Response fields (v4)

| Field | Description |
|-------|-------------|
| `data.success` | true when at least one plate was read; data.message says how many. |
| `data.processing_time` | Time spent on the request in milliseconds (download, plate recognition and vehicle detection). v4 takes longer than v2 because of the vehicle detection, typically 0.5-1.5 s per image. With config.mode=fast there is no vehicle detection, and the time is close to v2. |
| `data.camera_id` | The camera_id you sent, unchanged. Present only when you sent one. |
| `data.timestamp` | The timestamp you sent, unchanged (a string stays a string, a number stays a number). Present only when you sent one. |
| `data.results[]` | One entry per plate, highest detection score first. |
| `data.results[].plate` | Plate text, upper case, letters A-Z and digits only (no spaces or dashes). |
| `data.results[].score` | Reading confidence 0-1 (mean of the per-character confidences). |
| `data.results[].candidates[]` | Up to 5 readings {plate, score}, best first; the first one is the plate. |
| `data.results[].region` | {code, score}: code is the ISO 3166-1 alpha-2 country of the plate (for example UA, PL, RO), read from the plate format. score 0-1 is the reading confidence multiplied by how specific the format is; when the same format is used in several countries (for example LT, MD and HU share ABC123) the score is split between them and is low. null when the text matches no known format. Covered: UA, BG, BY, RO, ES, CZ, BE, EE, HU, TR, PL, SK, LV, LT, MD, IT, FR, RS, DE. |
| `data.results[].box` | Plate position in pixels: xmin, ymin, xmax, ymax. |
| `data.results[].dscore` | Plate detection confidence 0-1. |
| `data.results[].vehicle` | {type, score, box}: the vehicle that carries the plate. type is car, bus, truck or motorcycle; score is the detection confidence 0-1; box is the vehicle position in pixels (xmin, ymin, xmax, ymax). Coaches seen from the front and minibuses or vans can be typed truck (the detector has no van class). null when no detected vehicle contains the plate, and always null with config.mode=fast. |
| `error.code` | 400: image_required, bad_json, bad_base64, one_image_only, bad_image_url, image_url_not_allowed, image_url_fetch_failed, image_url_timeout, invalid_image, unsupported_image_type, unsupported_content_type, upload_failed, bad_regions, bad_camera_id, bad_timestamp, bad_config, bad_threshold, bad_mode. 404: no_plate_detected (also when config.threshold leaves no result). 413: payload_too_large. 500: engine_error. |


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
*Auto-generated 2026-10-28 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
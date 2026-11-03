# Pet Recognition API

Find out if a photo shows a dog, and which breed. POST one image (JPEG, PNG, WebP, BMP or TIFF, max 8 MB, at least 200 pixels on the shorter side) as a JSON body with image_url or image_base64, as a multipart/form-data upload in the field "image", or as the raw bytes with an image/* Content-Type. A dog detector checks the photo first: no dog means 404 no_dog_detected. Then two breed models give up to 3 breeds from a list of 60 common breeds, with scores. Dogs only: cats and other animals are not recognised. The API recognises the breed, not the individual animal. Typical uses: pet apps, smart pet doors, and Home Assistant or other home-automation platforms. The result is a classification with a score, not a guarantee: your system sets the threshold and decides what to do. Images are processed in memory and never stored.

**Endpoint:** `GET /api/v2/data/pet-recognition`
**Quota class:** heavy — 200/day (Explorer), 10000/day (PAYG)

---

## Parameters

| Name | Description |
|------|-------------|
| `image_url` | (JSON body) Public http(s) URL of the image. Max 8 MB, max 3 redirects, 10 s download limit. |
| `image_base64` | (JSON body) The image as base64 (a data:image/...;base64, prefix is allowed). Send image_url or image_base64, not both. |


## Example

```bash
curl "https://nakordoni.eu/api/v2/data/pet-recognition" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Response fields

Inside the `data` object of the envelope. A field is `null`, absent or an empty list when we hold no value for it — never a placeholder.

| Field | Description |
|-------|-------------|
| `data.success` | true when a dog was found; data.message says the result in words. |
| `data.processing_time` | Time spent on the request in milliseconds (download, dog detection and breed recognition). |
| `data.species` | Always "dog" (the only species we recognise). |
| `data.dog_score` | Confidence 0-1 of the dog detector. |
| `data.breed` | {id, name, score}: the breed, set only when both breed models agree on it; null when they do not agree. Use data.breeds then. |
| `data.breeds[]` | Up to 3 breeds {id, name, score}, most likely first. id is a stable slug (for example labrador). score is the main model score 0-1; it is relative, often low (0.05-0.2) even for a correct breed, so compare breeds by order, not by a fixed score. Empty when the dog matches none of the 60 breeds. |
| `data.models_agree` | true when both breed models chose the same first breed (then data.breed is set). |
| `error.code` | 400: image_required, bad_json, bad_base64, one_image_only, bad_image_url, image_url_not_allowed, image_url_fetch_failed, image_url_timeout, invalid_image, image_too_small, unsupported_image_type, unsupported_content_type, upload_failed. 404: no_dog_detected. 413: payload_too_large. 500: engine_error. 503: engine_unavailable or engine_busy, with a Retry-After header and error.retry_after (seconds); when the engine is already paused before your call starts, the 503 engine_unavailable is not counted against your quota. |


## Response envelope

```json
{
  "ok": true,
  "api_version": "1",
  "product": "pet-recognition",
  "attribution": "Data by nakordoni.eu",
  "data": [ ... ]
}
```

---

Full docs: https://nakordoni.eu/en/developers/docs#pet-recognition
*Auto-generated 2026-11-03 — regenerate: `sudo -u www-data php /var/www/html/helpers/push_github_docs.php`*
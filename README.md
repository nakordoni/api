# Nakordoni Developer API

Real-time border crossing data for Ukraine and its neighbours — queue lengths, wait estimates, forecasts, fuel prices, driver POIs and more.

[![API Status](https://img.shields.io/badge/API-live-brightgreen)](https://nakordoni.eu/en/status)
[![Docs](https://img.shields.io/badge/docs-nakordoni.eu-blue)](https://nakordoni.eu/en/developers/docs)

## Quick start

```bash
# 1. Get a free key → https://nakordoni.eu/en/developers/signup
# 2. Call any product:
curl "https://nakordoni.eu/api/v1/data/queue?ppid=id_13" \
  -H "Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX"
```

## Base URL

```
https://nakordoni.eu/api/v1/data/
```

## Authentication

Send your key in the `Authorization` header:

```
Authorization: Bearer NKD-DEV-XXXX-XXXX-XXXX
```

Every response includes your remaining quota:

```json
{
  "ok": true,
  "api_version": "1",
  "product": "queue",
  "attribution": "Data by nakordoni.eu",
  "data": [...],
  "snapshot": { "queue_now": 3, "wait_min": 30 },
  "usage": { "requests_today": 12, "limit_today": 200, "remaining": 188 }
}
```

## Plans

| Plan | Standard calls/day | Forecast+Stats/day | QPS |
|---|---|---|---|
| **Explorer** (free) | 200 | 50 | 2 |
| **Pay As You Grow** | custom | custom | 10 |

[Sign up for a free Explorer key →](https://nakordoni.eu/en/developers/signup)

## Products

| Product | Endpoint | Class |
|---|---|---|
| [API Status](docs/status.md) | `GET /api/v1/data/status` | free/public |
| [Checkpoints Directory](docs/checkpoints.md) | `GET /api/v1/data/checkpoints` | standard |
| [Border Queue](docs/border.md) | `GET /api/v1/data/border/{origin}/{dest}/{type}` | standard |
| [Checkpoint Search](docs/search.md) | `GET /api/v1/data/search` | standard |
| [Live Queue](docs/queue.md) | `GET /api/v1/data/queue` | standard |
| [Hourly Statistics](docs/stats.md) | `GET /api/v1/data/stats` | forecast+stats |
| [Best Time to Cross](docs/day-stats.md) | `GET /api/v1/data/day-stats` | forecast+stats |
| [Queue Forecast](docs/forecast.md) | `GET /api/v1/data/forecast` | forecast+stats |
| [Alternatives](docs/alternatives.md) | `GET /api/v1/data/alternatives` | standard |
| [Data Freshness](docs/update-info.md) | `GET /api/v1/data/update-info` | standard |
| [EU Fuel Prices](docs/fuel.md) | `GET /api/v1/data/fuel` | standard |
| [Driver POIs](docs/pois.md) | `GET /api/v1/data/pois` | standard |
| [Truck Driving Bans](docs/truck-bans.md) | `GET /api/v1/data/truck-bans` | standard |
| [Trading Sundays](docs/trading-sundays.md) | `GET /api/v1/data/trading-sundays` | standard |
| [Bus Carrier Stats](docs/bus-carriers.md) | `GET /api/v1/data/bus-carriers` | standard |
| [Road Conditions](docs/road-conditions.md) | `GET /api/v1/data/road-conditions` | standard |
| [Border AI Assistant](docs/assistant.md) | `GET /api/v1/data/assistant` | forecast+stats |

## Code Samples

**JavaScript**
```js
const res = await fetch('https://nakordoni.eu/api/v1/data/queue?ppid=id_13', {
  headers: { 'Authorization': 'Bearer NKD-DEV-XXXX-XXXX-XXXX' }
});
const { data, snapshot } = await res.json();
console.log(`Queue: ${snapshot.queue_now} cars, ~${snapshot.wait_min} min wait`);
```

**Python**
```python
import os, requests

KEY = os.environ["NKD_DEV_KEY"]
BASE = "https://nakordoni.eu/api/v1/data"

# Get all UA→PL car crossings sorted by queue
r = requests.get(f"{BASE}/border/1/2/4",
                 headers={"Authorization": f"Bearer {KEY}"})
for cp in r.json()["data"]:
    print(cp["name"], "—", cp["queue_now"], "cars,", cp["wait_min"], "min")
```

**PHP**
```php
$key  = getenv('NKD_DEV_KEY');
$resp = file_get_contents('https://nakordoni.eu/api/v1/data/forecast?ppid=id_13', false,
    stream_context_create(['http' => ['header' => "Authorization: Bearer $key"]]));
$json = json_decode($resp, true);
```

## Country Codes

| Code | Country | Code | Country |
|---|---|---|---|
| 1 | Ukraine | 2 | Poland |
| 3 | Slovakia | 4 | Hungary |
| 5 | Romania | 6 | Moldova |
| 7 | Belarus | 8 | Lithuania |
| 9 | Latvia | 10 | Estonia |
| 11 | Slovenia | 12 | Bulgaria |
| 13 | Serbia | 14 | Turkey |
| 15 | North Macedonia | 16 | Croatia |
| 17 | Bosnia & Herzeg. | 18 | Germany |
| 19 | Greece | 20 | Italy |

## Vehicle / Crossing Types

| Code | Type |
|---|---|
| 4 | Car (UA → EU) |
| 5 | Car — tax-free lane (EU → UA) |
| 6 | Bus |
| 7 | Pedestrian |
| 8 | Truck < 7.5t |
| 9 | Truck |

## Attribution

Explorer-plan integrations must display a visible **"Data by nakordoni.eu"** link on the page where data is shown. Pay As You Grow customers may omit it.

## Links

- 🌐 [Developer portal](https://nakordoni.eu/en/developers)
- 📖 [Full interactive docs](https://nakordoni.eu/en/developers/docs)
- 📋 [API changelog](https://nakordoni.eu/en/developers/changelog)
- 🤖 [Raw Markdown for AI assistants](https://nakordoni.eu/api/v1/docs.md)
- 🎫 [Support tickets](https://nakordoni.eu/en/developers/tickets)
- 📊 [Live status](https://nakordoni.eu/en/status)

## Support

Open a ticket in your [developer dashboard](https://nakordoni.eu/en/developers/tickets) or file a GitHub issue.

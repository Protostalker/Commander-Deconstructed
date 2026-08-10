# Commander-Deconstructed
VERIFONE_COMMANDER_NAXML_API_REFERENCE

# Handoff: Verifone Commander Read-Only Connector

> **For:** Claude (new session) or any developer picking this up cold
>
> **What this is:** A complete spec to build a Dockerized read-only REST API proxy for the Verifone Commander POS system. The goal is to expose Commander fuel pricing and sales data as clean JSON endpoints so downstream tools (TLS Decoded, dashboards, monitoring systems, spreadsheets) can consume it without ever touching NAXML, session tokens, or self-signed certs.
>
> **Source codebase you have:** `commander-console/` — a working read+write connector with React UI built against the real Commander unit. **Use it as your starting point.** The NAXML client, XML parsers, and FastAPI structure are all proven against the live system. Your job is to fork the connector layer, strip all write operations, and reshape it into a clean read-only proxy. Do NOT modify the original `commander-console/` project.

---

## Start Here: Using commander-console as Your Source

The `commander-console/connector/` directory has everything you need already working against the real Commander. Don't rebuild from scratch — cannibalize it.

### Files to copy and use directly (minimal changes needed)

| File | What it does | Changes needed |
|------|-------------|----------------|
| `connector/commander_client.py` | NAXML HTTP client, session token management, auto-relogin | Remove `update_prices()` and `push_prices_to_dispensers()` methods. Add `get_pumps()` and `get_config()` methods using same `_naxml()` pattern. |
| `connector/naxml_parser.py` | XML → Python: parses `vfuelprices`, `vfueltotals`, login tokens | Add `parse_pump_totals()` for `vmaintfprht` response. Keep everything else. |
| `connector/models.py` | Pydantic response models | Keep `FuelGrade`, `FuelTotals`, `FuelGradeTotals`. Remove write-related models (`GradePriceUpdate`, `PriceUpdateRequest`, `PushResponse`). Add `PumpTotals` model. |
| `connector/cache.py` | In-memory price cache with TTL | Use as-is. |
| `connector/config.py` + `config/commander.yaml` | Config loading | Simplify — remove mock config, keep commander + polling sections. |
| `connector/main.py` | FastAPI app + lifespan + background tasks | Remove mock mode, remove write routers, register new read-only routers. |

### Files to skip entirely (don't copy)

| File | Why |
|------|-----|
| `connector/naxml_builder.py` | Builds XML for write operations — not needed |
| `connector/mock_driver.py` | Mock mode — not needed for prod connector |
| `connector/routers/prices.py` | Has write endpoints (POST /prices, POST /prices/push) — rewrite from scratch as read-only |
| `connector/routers/mock.py` | Mock toggle endpoints — not needed |
| `frontend/` | Entire React app — this project has no frontend |
| `docker-compose.yml` | References frontend service — write a new simpler one |

### What you're building on top of

The existing code has proven (on a live Commander unit) that:
- The NAXML endpoint is `POST https://<host>/cgi-bin/NAXML?`
- Auth body format: `cmd=validate&user=X&passwd=Y\n\n` (the `\n\n` is mandatory)
- Token is in `<cookie>32hexchars</cookie>` of the login response
- Authenticated request body: `cmd=X&cookie=TOKEN\n\n[optional_xml]`
- `vfueltotals` needs `&period=N` as a URL param in the body (NOT in XML)
- Session expiry returns `<faultCode>CGIPortal.LoginRequired</faultCode>`
- SSL cert is self-signed → `verify=False`

All of this is already handled correctly in `commander_client.py`. Trust it.

---

## The Problem Being Solved

The Verifone Commander POS exposes all its data through a proprietary XML-over-HTTP protocol (NAXML) with session-token authentication. Every tool that wants Commander data currently has to:

1. Implement NAXML auth (login, extract hex token from XML response)
2. Handle session expiry and re-login
3. Deal with self-signed SSL certs
4. Parse namespace-heavy XML responses
5. Know the quirky request format (body ends with `\n\n`, period params go in body not XML, etc.)

This project eliminates all of that. Build one container that handles the Commander complexity internally and exposes clean, documented JSON endpoints to the local network. Any tool that can call a REST API gets Commander data for free.

```
[Verifone Commander POS]
        |
        | NAXML over HTTPS (self-signed cert, session tokens, XML)
        v
[commander-reader  :8200]   ← THIS PROJECT
  Docker container
  Python 3.12 / FastAPI
  Background polling every 60s
  In-memory cache + SQLite log
        |
        | Plain HTTP, clean JSON, no auth needed
        v
[TLS Decoded]   [Dashboards]   [Grafana]   [Scripts]   [Anything]
```

---

## Project Spec

### Name
`commander-reader`

### Language / Stack
- **Python 3.12**
- **FastAPI** — REST API framework, auto-generates OpenAPI/Swagger docs
- **httpx** — async HTTP client for Commander NAXML calls
- **aiosqlite** — async SQLite for price change history
- **pydantic** — response models / data validation
- **Docker + Docker Compose** — single-command deployment

### Constraints
- **READ ONLY.** No endpoints that write prices or push to dispensers. The Commander write commands (`ufuelprices`, `cfuelprices`) must not be called anywhere in this codebase.
- **No frontend.** This is a pure API service. Swagger UI at `/docs` is fine.
- **Stateless consumers.** Consumers call REST endpoints; they don't manage sessions or tokens.
- **Self-contained.** Single Docker Compose file, everything configured via env vars.

---

## Configuration

All configuration via environment variables:

| Variable                    | Default       | Description                              |
|-----------------------------|---------------|------------------------------------------|
| `COMMANDER_HOST`            | *(required)*  | Hostname or IP of Commander unit         |
| `COMMANDER_PORT`            | `443`         | HTTPS port                               |
| `COMMANDER_USERNAME`        | `MANAGER`     | Commander account username               |
| `COMMANDER_PASSWORD`        | *(required)*  | Commander account password               |
| `COMMANDER_VERIFY_SSL`      | `false`       | Set `true` only if cert is valid         |
| `POLL_INTERVAL_SECONDS`     | `60`          | How often to refresh price cache         |
| `SESSION_REFRESH_MINUTES`   | `25`          | Re-login if token is older than this     |
| `PORT`                      | `8200`        | Port to listen on inside container       |
| `LOG_LEVEL`                 | `INFO`        | Python logging level                     |
| `DB_PATH`                   | `/app/data/history.db` | SQLite database path          |

---

## API Endpoints

All responses are JSON. All errors follow FastAPI's standard `{"detail": "..."}` format.

### `GET /health`

Service health and Commander connection status.

```json
{
  "status": "ok",
  "commander_host": "smincgs.ddns.net",
  "connected": true,
  "session_authenticated": true,
  "session_age_seconds": 3421,
  "last_price_fetch": "2026-08-10T14:32:11Z",
  "last_price_fetch_age_seconds": 47,
  "poll_interval_seconds": 60
}
```

### `GET /prices`

Current fuel prices from cache (refreshed every `POLL_INTERVAL_SECONDS`).

```json
{
  "grades": [
    {
      "id": 1,
      "name": "REGULAR",
      "in_effect": { "cash": 5.579, "credit": 5.579 },
      "pending":   { "cash": 5.199, "credit": 5.299 }
    },
    {
      "id": 2,
      "name": "PLUS",
      "in_effect": { "cash": 5.779, "credit": 5.779 },
      "pending":   { "cash": 5.779, "credit": 5.779 }
    },
    {
      "id": 3,
      "name": "SUPER",
      "in_effect": { "cash": 5.979, "credit": 5.979 },
      "pending":   { "cash": 5.979, "credit": 5.979 }
    },
    {
      "id": 7,
      "name": "DIESEL#2",
      "in_effect": { "cash": 4.799, "credit": 4.799 },
      "pending":   { "cash": 4.799, "credit": 4.799 }
    }
  ],
  "from_cache": true,
  "fetched_at": "2026-08-10T14:32:11Z",
  "cache_age_seconds": 47
}
```

### `GET /prices/live`

Force-fetch prices directly from Commander (bypasses cache, slower).

Same response shape as `/prices` but `from_cache: false`.

### `GET /prices/history`

Price change log from SQLite.

Query params: `limit` (default 100), `offset` (default 0)

```json
[
  {
    "id": 42,
    "timestamp": "2026-08-10T06:00:01Z",
    "grade_name": "REGULAR",
    "grade_id": 1,
    "tier": "cash",
    "old_price": 5.579,
    "new_price": 5.199,
    "detected_by": "poll"
  }
]
```

> The service detects price changes automatically during each poll (by comparing cached vs freshly fetched prices) and logs them to SQLite. No write operations to Commander needed.

### `GET /totals`

Sales totals aggregated across all dispensers.

Query params: `period` — `shift`, `day`, `month`, or `year` (default `day`)

```json
{
  "period": "day",
  "period_begin": "2026-08-09T20:00:06-07:00",
  "period_seq_num": 506,
  "grades": [
    {
      "name": "REGULAR",
      "volume_gallons": 1543.755,
      "revenue_usd": 6624.21,
      "avg_price_per_gallon": 4.291
    },
    {
      "name": "DIESEL#2",
      "volume_gallons": 3298.346,
      "revenue_usd": 6086.09,
      "avg_price_per_gallon": 1.845
    }
  ],
  "total_volume_gallons": 5626.07,
  "total_revenue_usd": 16233.41
}
```

### `GET /pumps`

Per-pump, per-hose lifetime mechanical totals (odometer readings).

```json
{
  "pumps": [
    {
      "id": 1,
      "hoses": [
        {
          "id": 1,
          "product_name": "REGULAR",
          "total_volume_gallons": 183604.015,
          "total_revenue_usd": 921069.09,
          "total_transactions": 13397
        }
      ]
    }
  ],
  "site_total_volume_gallons": 734416.0,
  "site_total_revenue_usd": 3682276.38,
  "site_total_transactions": 53590
}
```

### `GET /config`

Fuel site configuration (grades, UOM, service levels, tier-2 scheduling).

```json
{
  "site_id": 1,
  "raw_xml": "<fuelcfg:fuelConfig ...>...</fuelcfg:fuelConfig>"
}
```

> Return raw XML for now — the fuelcfg schema is not fully documented. A future version can parse this into structured JSON once the schema is understood.

### `GET /metrics`

Prometheus-compatible metrics (optional but recommended).

```
# HELP commander_price_cash Current cash price per gallon
# TYPE commander_price_cash gauge
commander_price_cash{grade="REGULAR"} 5.579
commander_price_cash{grade="PLUS"} 5.779
...
commander_session_age_seconds 3421
commander_last_fetch_age_seconds 47
```

---

## File Structure

```
commander-reader/
├── Dockerfile
├── docker-compose.yml
├── docker-compose.override.yml.example
├── .env.example
├── README.md
│
└── app/
    ├── main.py              # FastAPI app, lifespan, background tasks
    ├── config.py            # Pydantic settings from env vars
    ├── commander_client.py  # NAXML HTTP client (login, naxml(), relogin logic)
    ├── naxml_parser.py      # XML → Python dicts (prices, totals, pumps, config)
    ├── cache.py             # In-memory price cache with TTL
    ├── history.py           # SQLite price change detection + log
    ├── models.py            # Pydantic response models
    └── routers/
        ├── __init__.py
        ├── health.py        # GET /health
        ├── prices.py        # GET /prices, /prices/live, /prices/history
        ├── totals.py        # GET /totals
        ├── pumps.py         # GET /pumps
        ├── config.py        # GET /config
        └── metrics.py       # GET /metrics (Prometheus format)
```

---

## Docker Setup

### `Dockerfile`

```dockerfile
FROM python:3.12-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY app/ ./app/

# Data directory for SQLite
RUN mkdir -p /app/data

EXPOSE 8200

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8200"]
```

### `requirements.txt`

```
fastapi==0.111.0
uvicorn[standard]==0.29.0
httpx==0.27.0
aiosqlite==0.20.0
pydantic==2.7.0
pydantic-settings==2.2.0
```

### `docker-compose.yml`

```yaml
version: '3.8'

services:
  commander-reader:
    build: .
    container_name: commander-reader
    ports:
      - "8200:8200"
    environment:
      - COMMANDER_HOST=${COMMANDER_HOST}
      - COMMANDER_PORT=${COMMANDER_PORT:-443}
      - COMMANDER_USERNAME=${COMMANDER_USERNAME:-MANAGER}
      - COMMANDER_PASSWORD=${COMMANDER_PASSWORD}
      - COMMANDER_VERIFY_SSL=${COMMANDER_VERIFY_SSL:-false}
      - POLL_INTERVAL_SECONDS=${POLL_INTERVAL_SECONDS:-60}
      - SESSION_REFRESH_MINUTES=${SESSION_REFRESH_MINUTES:-25}
      - LOG_LEVEL=${LOG_LEVEL:-INFO}
    volumes:
      - commander_data:/app/data
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8200/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 15s

volumes:
  commander_data:
    name: commander_reader_data
```

### `.env.example`

```bash
COMMANDER_HOST=smincgs.ddns.net
COMMANDER_PORT=443
COMMANDER_USERNAME=MANAGER
COMMANDER_PASSWORD=change_me
COMMANDER_VERIFY_SSL=false
POLL_INTERVAL_SECONDS=60
SESSION_REFRESH_HOURS=23
LOG_LEVEL=INFO
```

---

## NAXML API: Everything You Need to Know

> This is the complete protocol reference so you don't need to consult any other document.

### Endpoint

```
POST https://<COMMANDER_HOST>:<COMMANDER_PORT>/cgi-bin/NAXML?
Content-Type: application/x-www-form-urlencoded
```

The trailing `?` is part of the URL path — not a query string separator.
SSL certificate is self-signed — always use `verify=False`.

### Login

```
Body: cmd=validate&user=<USERNAME>&passwd=<PASSWORD>\n\n
```

Response:
```xml
<domain:credential xmlns:domain="urn:vfi-sapphire:np.domain.2001-07-01">
  <cookie>40596f0dd65b38f31a8e7d566dcdcdf9</cookie>  ← 32-char hex token
  <vs:site>1</vs:site>
  <funcList>validate,vfuelprices,vfueltotals,...</funcList>
</domain:credential>
```

Extract token: find `<cookie>...</cookie>`.

### Authenticated Requests

```
Body: cmd=<COMMAND>&cookie=<TOKEN>\n\n[<optional_xml>]
```

The `\n\n` (two newlines) is MANDATORY. It separates the URL-encoded header from the optional XML body.

### Adding URL Parameters (e.g. vfueltotals)

```
Body: cmd=vfueltotals&cookie=<TOKEN>&period=2\n\n
```

Some commands require inline URL params. Do NOT put these in the XML body.

### Commands to Implement

| Command        | Params                 | Returns                        |
|----------------|------------------------|--------------------------------|
| `validate`     | user, passwd           | Session token                  |
| `vfuelprices`  | —                      | All grades, Tier1+Tier2 prices |
| `vfueltotals`  | `&period=1\|2\|3\|4`   | Volume+revenue per dispenser/grade |
| `vmaintfprht`  | —                      | Lifetime pump totals           |
| `vfuelcfg`     | —                      | Site fuel config               |

Period values: 1=Shift, 2=Day, 3=Month, 4=Year

**DO NOT implement:** `ufuelprices`, `cfuelprices`, `ccarwashenable`, `ccarwashdisable`, or any `c`/`u` prefix commands. This service is read-only.

### Session Lifetime — Login On Demand

**Observed:** Commander sessions expire in approximately **30 minutes or less**. Do not assume a long-lived session.

**Pattern to implement:**
- Store token + timestamp at login
- Before every NAXML call: if `time.time() - token_at > 25 * 60`, re-login first
- If `CGIPortal.LoginRequired` fault comes back anyway: re-login and retry once
- No need for a background keep-alive task — the per-call age check handles everything
- Re-login is cheap (one HTTP round trip); don't try to avoid it

When session expires, Commander returns:
```xml
<VFI:Response>
  <VFI:Fault>
    <faultCode>CGIPortal.LoginRequired</faultCode>
    <faultString>Session has expired</faultString>
  </VFI:Fault>
</VFI:Response>
```

On `CGIPortal.LoginRequired`: re-login, then retry the original request once.
On any other fault code: raise an error, don't retry.

### NAXML Client (reference implementation)

```python
import httpx, time, asyncio

class CommanderClient:
    NAXML_PATH = "/cgi-bin/NAXML?"
    FAULT_LOGIN_REQUIRED = "CGIPortal.LoginRequired"

    def __init__(self, host, port, username, password, verify_ssl=False, timeout=10):
        self.base_url = f"https://{host}:{port}"
        self.username = username
        self.password = password
        self._http = httpx.AsyncClient(verify=verify_ssl, timeout=timeout)
        self._token: str | None = None
        self._token_at: float = 0.0

    async def login(self) -> bool:
        body = f"cmd=validate&user={self.username}&passwd={self.password}\n\n"
        try:
            r = await self._http.post(
                self.base_url + self.NAXML_PATH,
                content=body.encode(),
                headers={"Content-Type": "application/x-www-form-urlencoded"},
            )
        except httpx.RequestError:
            return False
        if r.status_code != 200:
            return False
        token = self._extract(r.text, "cookie")
        if not token:
            return False
        self._token = token
        self._token_at = time.time()
        return True

    async def naxml(self, cmd: str, xml_body: str = "", **params) -> str:
        for attempt in range(2):
            if not self._token:
                await self.login()
            extra = "".join(f"&{k}={v}" for k, v in params.items())
            body = f"cmd={cmd}&cookie={self._token}{extra}\n\n{xml_body}"
            r = await self._http.post(
                self.base_url + self.NAXML_PATH,
                content=body.encode(),
                headers={"Content-Type": "application/x-www-form-urlencoded"},
            )
            r.raise_for_status()
            fault = self._extract(r.text, "faultCode")
            if fault == self.FAULT_LOGIN_REQUIRED and attempt == 0:
                self._token = None
                continue
            if fault:
                raise RuntimeError(f"Commander fault: {fault}")
            return r.text
        raise RuntimeError("Failed after relogin")

    # Convenience methods
    async def get_prices(self):    return await self.naxml("vfuelprices")
    async def get_totals(self, p): return await self.naxml("vfueltotals", period=p)
    async def get_pumps(self):     return await self.naxml("vmaintfprht")
    async def get_config(self):    return await self.naxml("vfuelcfg")

    @staticmethod
    def _extract(text, tag):
        s = text.find(f"<{tag}>")
        e = text.find(f"</{tag}>", s)
        return text[s+len(tag)+2:e].strip() if s != -1 and e != -1 else None
```

---

## Implementation Notes

### Startup Sequence

1. Load config from env vars
2. Attempt Commander login → store token
3. Fetch initial prices → populate cache
4. Start background tasks:
   - **Price poll loop:** every `POLL_INTERVAL_SECONDS`, fetch `vfuelprices`, compare with cache, log any changes to SQLite, update cache
   - **Session refresh loop:** every `SESSION_REFRESH_HOURS * 3600s`, proactively re-login
5. Start serving API requests

### Price Change Detection

On every poll, compare freshly fetched prices against cached prices. If any grade's `in_effect.cash`, `in_effect.credit`, `pending.cash`, or `pending.credit` has changed, write a row to the SQLite `price_history` table. This gives consumers a change log without needing to subscribe to events.

```sql
CREATE TABLE IF NOT EXISTS price_history (
    id           INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp    TEXT NOT NULL,
    grade_id     INTEGER NOT NULL,
    grade_name   TEXT NOT NULL,
    tier         TEXT NOT NULL,   -- 'in_effect_cash', 'in_effect_credit', 'pending_cash', 'pending_credit'
    old_price    REAL,
    new_price    REAL NOT NULL,
    detected_by  TEXT NOT NULL DEFAULT 'poll'
);
```

### Graceful Degradation

If the Commander is unreachable:
- `/health` returns `connected: false` but HTTP 200
- `/prices` returns the last cached prices with `stale: true` and `cache_age_seconds: N`
- `/totals`, `/pumps`, `/config` return HTTP 502 with a clear error message
- The background poll loop keeps retrying; when the Commander comes back, the cache updates automatically

### Thread Safety

Use a single `asyncio.Lock` around the session token update. All NAXML calls go through the same `CommanderClient` instance. FastAPI + uvicorn run on a single asyncio event loop, so `asyncio.Lock` is sufficient — no threading primitives needed.

---

## TLS Decoded Integration

TLS Decoded is a POS transaction analysis tool that can be configured to pull data from HTTP endpoints. Once `commander-reader` is running, point TLS Decoded at:

```
http://<server-running-commander-reader>:8200/prices
http://<server-running-commander-reader>:8200/totals?period=day
```

The response is plain JSON over plain HTTP — no auth, no NAXML, no SSL issues. TLS Decoded (or any similar tool) just polls these URLs on whatever interval it needs.

**Recommended polling intervals for TLS Decoded:**
- `/prices` — every 60s (matches poll loop; prices rarely change more than once/day)
- `/totals?period=shift` — every 5 minutes (shift totals accumulate through the day)
- `/health` — every 30s (connection monitoring)

---

## Stretch Goals / Ideas

Once the base connector is working, here are extensions worth building:

### Webhook / Push Notifications
Add a `POST /webhooks` endpoint where consumers register a URL. When the poll loop detects a price change, POST to all registered URLs. Useful for alerting when prices change.

```json
// Webhook payload on price change
{
  "event": "price_changed",
  "timestamp": "2026-08-10T14:00:00Z",
  "grade_name": "REGULAR",
  "tier": "in_effect_cash",
  "old_price": 5.579,
  "new_price": 5.199
}
```

### Scheduled Price Snapshots
Every N hours (configurable), snapshot current prices and totals to SQLite. Enables:
- Historical price tracking over weeks/months
- "What were prices last Tuesday at 6pm?" queries
- CSV export of price history

### Prometheus + Grafana
`/metrics` endpoint in Prometheus exposition format. Pair with a `docker-compose.yml` that includes Prometheus + Grafana containers:

```yaml
services:
  commander-reader:
    # ... as above
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
```

This gives you a full dashboard with price history graphs and pump total trends — for free.

### Price Comparison / Competitor Tracking
Add a background task that fetches nearby competitor prices from a gas price API (GasBuddy, AAA, etc.) and stores them alongside Commander prices. Expose via `/compare` endpoint. Useful for dynamic pricing decisions.

### Monthly In-House Sales Report
The user mentioned possibly adding monthly in-house sales data extraction. Once `vmaintfprht` (pump totals) and `vfueltotals` (period totals) are working, a monthly snapshot job can:
1. On the 1st of each month, call `vfueltotals&period=4` (year) and `vfueltotals&period=3` (month)
2. Store to SQLite with a `snapshot_date` field
3. Expose via `GET /reports/monthly?year=2026&month=07` → JSON or CSV

---

## What NOT to Build (This Session)

To keep scope clear:
- **No write endpoints.** No `ufuelprices`, no `cfuelprices`, no car wash control.
- **No React frontend.** The existing `commander-console` project has a UI. This is pure API.
- **No external authentication.** This runs on an internal LAN. HTTP with no auth is fine. If auth is needed later, add an API key via a middleware in a future iteration.

---

## Delivery

When done, the user should be able to:

```bash
cd commander-reader
cp .env.example .env
# edit .env with real COMMANDER_HOST and COMMANDER_PASSWORD
docker compose up -d

# Verify
curl http://localhost:8200/health
curl http://localhost:8200/prices
curl "http://localhost:8200/totals?period=day"
```

And see live Commander data as clean JSON with no NAXML knowledge required.

Swagger UI available at `http://localhost:8200/docs` for exploration.

---

## Suggested Build Order

1. Copy `commander_client.py`, `naxml_parser.py`, `models.py`, `cache.py` from `commander-console/connector/` into `commander-reader/app/`
2. Strip write methods from `commander_client.py`, add `get_pumps()` and `get_config()`
3. Add `parse_pump_totals()` to `naxml_parser.py`
4. Write new `main.py` (lifespan + background poll loop + session refresh) — simpler than commander-console's since no mock mode
5. Write read-only routers one at a time: health → prices → totals → pumps → config → metrics
6. Write `Dockerfile` and `docker-compose.yml`
7. Add price change detection in the poll loop + SQLite write
8. Test with `docker compose up`, hit `/docs`, verify all endpoints return real data

---

*Handoff prepared: August 2026*

# Changelog

## 0.1.6.8
### ✅ Release cleanup (publish-ready)
- Version scheme normalized to **0.1.6.8** (no extra suffix).
- Package cleaned (no `__pycache__` / `.pyc`).
- Manifest validated for HACS (keys sorted, valid JSON).
- Short README disclaimer for **unofficial / private API** usage.

## 0.1.6.7
### 🧾 Logging & Diagnostics
- Added verbose logs for:
  - outbound MQTT queueing / coalescing / sending
  - status polling debounce + bulk (0x0C) throttling decisions
  - Work Schedule publish + parsing
  - HVAC Sync evaluations and actions
- Added diagnostic attributes on MQTT status sensor:
  - `last_tx_*`, `outbox_len`, `last_error`, `hvac_sync_*`

### ⏱️ Work run/stop limit
- UI + validation capped at **999 seconds** for:
  - Work run (seconds)
  - Work stop (seconds)
- Values above 999 are clamped and logged.

## 0.1.6.6
### 🌬️ HVAC Sync (Home Assistant local control)
- User can pick a thermostat (`climate.*`) and sync diffuser with **cooling** (`hvac_action: cooling`).
- Schedule controls: days of week + start/end time.
- Optional on/off delays to prevent rapid toggling.

## 0.1.6.5
### 🔒 Stability / “polite” behavior (private API friendly)
- Rate limiting outbound MQTT (default 1s min interval, burst 3).
- Coalescing / deduplication of repeated commands.
- Debounced `request_status()` (min 60s).
- Throttled bulk status `0x0C` (max every 6h, or when stale).
- Reduced reconnect “startup spam” (no automatic 0x05/0x0C unless needed).
- HTTP login uses standard headers + explicit handling of 401/403/429 with controlled backoff.

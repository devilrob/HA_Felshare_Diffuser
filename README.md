# Felshare Diffuser (Cloud MQTT) — Home Assistant Custom Integration

Control Felshare waterless diffusers via the Felshare cloud MQTT service.

> ⚠️ **Disclaimer (unofficial / private API)**: Not affiliated with Felshare. This integration uses undocumented/private endpoints and may break at any time. Use at your own risk and respect Felshare’s terms.

## Features

- **Power** switch
- **Fan** switch
- Oil **consumption**, **capacity**, and **remaining oil** controls
- Work schedule controls:
  - Enable/disable schedule
  - Start/End time (HH:MM)
  - Run/Stop seconds
  - Day-of-week mask (Mon–Sun)
- Diagnostics sensor with last seen/topic/payload + timestamps
- **Refresh status** button (best-effort cloud status request)

## Hardening / Stability

- Outbound MQTT publish queue with **rate limiting** + **coalescing**
- Debounced `request_status()` and throttled bulk (0x0C)
- Avoids “startup spam” on MQTT reconnects
- More polite HTTP headers + safer handling of `401/403/429`

## HVAC Sync

If you have a thermostat integrated in Home Assistant (for example **Nest Gen 3**), you can
optionally make the diffuser **follow active cooling**:

- Diffuser turns **ON** only when the thermostat reports `hvac_action: cooling`
- Diffuser turns **OFF** when cooling stops
- Optional schedule window (days + start/end time)
- Built-in **on/off delays** (defaults 60s) to avoid rapid toggles on short-cycles

### Configure from your dashboard (recommended)

The HVAC Sync settings are exposed as entities so you can place them in your own Lovelace
card (no Options Flow required):

- `switch.*hvac_sync` (enable/disable)
- `select.*hvac_sync_thermostat` (pick any `climate.*`)
- `time.*hvac_sync_start` / `time.*hvac_sync_end`
- `switch.*hvac_sync_day_*` (Mon..Sun)
- `number.*hvac_sync_on_delay_s` / `number.*hvac_sync_off_delay_s` (optional)

## Installation (HACS — custom repository)

1. In Home Assistant, open **HACS → Integrations → ⋮ → Custom repositories**.
2. Add your GitHub repo URL (this repository) and choose **Category: Integration**.
3. Install the integration from HACS.
4. Restart Home Assistant.

## Setup

1. Go to **Settings → Devices & services → Add integration**.
2. Search for **Felshare (Cloud MQTT)**.
3. Enter your Felshare app **email** and **password**.
4. Select the device from the list.

## Options (recommended defaults)

In **Settings → Devices & services → Felshare → Configure**:

- **Polling interval (minutes)**: **30–60** (or **0** if MQTT is stable)
- **Min publish interval (seconds)**: **1.0**
- **Max burst messages**: **3**
- **Min request_status interval (seconds)**: **60**
- **Bulk status interval (hours)**: **6**
- **Startup stale threshold (minutes)**: **30**

## Notes

- This integration is **cloud-based** (Felshare cloud MQTT), not local.
- Home Assistant UI icons/branding shown in the official integrations list come from the
  `home-assistant/brands` repository. For a HACS custom repo, HACS will use the `icon.png`
  and `logo.png` in this repository for display.

## Support

- Issues: use the GitHub issue tracker for this repository.

## Disclaimer

This is an unofficial community integration.

## Debug logging

To see detailed logs (queueing/coalescing, WorkTime changes, HVAC Sync decisions), add this to your `configuration.yaml` and restart Home Assistant:

```yaml
logger:
  logs:
    custom_components.felshare: debug
```

Then check **Settings → System → Logs**.
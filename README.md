# Smart PIR Radar Outdoor Security Node

[![ESPHome](https://img.shields.io/badge/ESPHome-2023.11%2B-blue.svg)](https://esphome.io/)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2023.11%2B-green.svg)](https://www.home-assistant.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A dual-sensor outdoor security node built on ESP8266 (ESP-01) that combines PIR (HC-SR501) and Radar (RCWL0516) sensors for high-confidence intrusion detection. Designed for seamless integration with Home Assistant via ESPHome.

---

## Features

- **Dual-Sensor Fusion**: Combines PIR and Radar motion detection for reduced false positives
- **ESP-01 Optimized**: Minimal hardware footprint with maximum reliability
- **Night-Aware Detection**: Adjusts PIR delay thresholds based on day/night cycle
- **Confirmed Intrusion Output**: Template binary sensor triggers only when BOTH sensors detect simultaneously
- **Over-The-Air Updates**: Flash firmware wirelessly via Home Assistant or ESPHome Dashboard
- **Fallback WiFi AP**: Built-in access point for recovery when primary WiFi is unavailable
- **Captive Portal**: Automatic recovery page on boot

## Hardware Required

| Component | Quantity | Notes |
|-----------|----------|-------|
| ESP-01 (ESP8266) | 1 | Core controller module |
| HC-SR501 PIR Sensor | 1 | Passive Infrared motion detector |
| RCWL0516 Radar Module | 1 | Microwave radar motion detector |
| 5V Power Supply | 1 | For sensors (ESP-01 uses 3.3V) |
| Breadboard/Jumper Wires | 1 set | For prototyping |

## ESPHome Version Requirements

- **ESPHome**: 2023.11 or later
- **Home Assistant**: 2023.11 or later (recommended)

## Wiring and Pin Mapping

### ESP-01 Pinout

```
┌─────────────┐
│   ESP-01    │
│             │
│ GPIO0  ──── │ (NOT USED - boot critical, keep HIGH)
│ GPIO2  ──── │ ──► HC-SR501 PIR OUTPUT
│ GPIO3  ──── │ ──► RCWL0516 RADAR OUTPUT (RX as GPIO)
│   GND    ── │ ──► Common GND (all devices)
│   3.3V   ── │ ──► 3.3V Power (ESP-01 only)
└─────────────┘

┌───────────────┐     ┌───────────────┐
│ HC-SR501 PIR  │     │ RCWL0516 Radar│
│               │     │               │
│ VCC      5V   │     │ VCC      5V   │
│ GND     GND   │     │ GND      GND  │
│ OUT    GPIO2  │     │ OUT     GPIO3 │
└───────────────┘     └───────────────┘

NOTE: All GND lines must be common/shared.
```

### Pin Summary

| ESP-01 Pin | Connection | Pull-up/Pull-down |
|------------|------------|-------------------|
| GPIO0 | Not used (keep HIGH at boot) | - |
| GPIO2 | HC-SR501 PIR Output | Internal pull-up enabled |
| GPIO3 | RCWL0516 Radar Output | No pull resistor |

## Installation

### 1. Prepare Secrets File

Copy the example secrets file and fill in your values:

```bash
cp secrets.example.yaml secrets.yaml
```

Edit `secrets.yaml` with your credentials:

```yaml
wifi_ssid: "your_wifi_network"
wifi_password: "your_wifi_password"
ota_password: "your_ota_password"
api_encryption_key: "generate_with_esphome secret"
```

### 2. Compile the Firmware

```bash
esphome compile SmartPIRRadarV01.yaml
```

### 3. Upload to Device

```bash
esphome upload SmartPIRRadarV01.yaml
```

Or use Home Assistant ESPHome Dashboard for OTA upload.

## Home Assistant Integration

### Automatic Discovery

After flashing, the device will:

1. Connect to your WiFi network
2. Register with Home Assistant via ESPHome API
3. Expose the following entities:

| Entity | Type | Description |
|--------|------|-------------|
| `pir_motion` | Binary Sensor | PIR sensor detection state |
| `radar_motion` | Binary Sensor | Radar sensor detection state |
| `confirmed_intrusion` | Binary Sensor | Dual-sensor confirmed intrusion |
| `outdoor_security_node_01_status` | Sensor | Device online/offline status |
| `restart_device` | Switch | Remote device restart |

### Automation Example

```yaml
automation:
  - alias: "Intrusion Alert"
    trigger:
      platform: state
      entity_id: binary_sensor.confirmed_intrusion
      to: "on"
    action:
      - service: notify.mobile_app_your_phone
        data:
          message: "Intrusion detected outdoors!"
      - service: light.turn_on
        target:
          entity_id: light.outdoor_light
```

## Usage

### Normal Operation

1. Device boots and connects to WiFi
2. Sensors continuously monitor for motion
3. `pir_motion` and `radar_motion` report individual detections
4. `confirmed_intrusion` triggers only when BOTH sensors detect simultaneously (high-confidence event)

### Night Mode

During nighttime, the PIR sensor delay increases from 800ms to 1000ms for reduced sensitivity.

### Remote Restart

Use the `restart_device` switch in Home Assistant to reboot the ESP-01 remotely.

## Troubleshooting

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Device won't connect to WiFi | Incorrect SSID/password | Verify `secrets.yaml` values |
| PIR sensor not detected | Wiring issue or GPIO conflict | Check GPIO2 connection; ensure pull-up is enabled |
| Radar sensor triggers falsely | Sensitivity too high | Adjust RCWL0516 potentiometer |
| OTA updates fail | Network issue | Use captive portal or wired upload |
| `confirmed_intrusion` never triggers | Sensors not synchronized | Check individual sensor states first |
| Boot loop | GPIO0 floating | Ensure GPIO0 is pulled HIGH during boot |

## License

This project is licensed under the MIT License. See [`LICENSE`](LICENSE) for details.

## Repository Structure

```
SmartPIRRadarSecurityNode/
├── SmartPIRRadarV01.yaml    # ESPHome device configuration
├── secrets.example.yaml     # Example secrets (safe to share)
├── secrets.yaml             # Your real secrets (NOT committed)
├── .gitignore               # Git ignore rules
├── README.md                # This file
├── LICENSE                  # MIT License
└── docs/
    ├── architecture.md      # System architecture
    ├── hardware.md          # Hardware details
    ├── installation.md      # Installation guide
    ├── configuration.md     # Configuration reference
    └── troubleshooting.md   # Troubleshooting guide
```

---

**Built with ESPHome and Home Assistant**

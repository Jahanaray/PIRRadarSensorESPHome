# System Architecture

## Overview

The Smart PIR Radar Security Node is a dual-sensor motion detection system built on the ESP8266 platform. It uses sensor fusion to reduce false positives by requiring confirmation from both PIR and Radar sensors before triggering a high-confidence intrusion event.

## Block Diagram

```
┌──────────────┐     ┌──────────────┐
│ HC-SR501 PIR │     │ RCWL0516     │
│ Sensor       │     │ Radar Module │
│ (5V)         │     │ (5V)         │
│              │     │              │
│ OUT ─────────┼─────► GPIO2        │
└──────────────┘     │               │
                     │   ESP-01     │
                     │   (3.3V)     │
                     │               │
                     │ OUT ─────────┼────► GPIO3
                     └──────────────┘
```

## Sensor Fusion Logic

### Individual Sensors

| Sensor | Technology | Detection Method | Delay On | Delay Off |
|--------|-----------|------------------|----------|-----------|
| PIR (HC-SR501) | Infrared | Thermal changes | 800ms (night) / 1000ms (day) | 1s |
| Radar (RCWL0516) | Microwave | Doppler shift | 2.8s | 1s |

### Confirmed Intrusion (Template Sensor)

The `confirmed_intrusion` binary sensor uses a logical AND of both sensors:

```
confirmed_intrusion = pir_motion AND radar_motion
```

Filtering:
- **Delayed On**: 200ms (debounce)
- **Delayed Off**: 10s (hold state for automation triggers)

## Night Mode Adaptation

The system reads the `sun.sun` entity from Home Assistant to determine day/night status:

- **Nighttime**: PIR delay_on = 800ms (more responsive)
- **Daytime**: PIR delay_on = 1000ms (less sensitive to transient events)

## Network Architecture

```
┌──────────────┐     mDNS/ESPHome     ┌─────────────────┐
│   ESP-01     │ ◄──────────────────► │ Home Assistant  │
│              │      API Discovery    │                 │
│ WiFi Station │                       │ Auto-discovery  │
│ Fallback AP  │                       │ OTA Updates     │
└──────────────┘                       └─────────────────┘
```

## Power Architecture

| Component | Voltage | Current (typical) |
|-----------|---------|-------------------|
| ESP-01 | 3.3V | ~80mA (WiFi active) |
| HC-SR501 | 5V | ~50mA |
| RCWL0516 | 5V | ~45mA |

## Firmware Flow

1. **Boot**: ESP-01 initializes, connects to WiFi
2. **Discovery**: Registers with Home Assistant via ESPHome API
3. **Monitoring**: Continuously reads PIR and Radar GPIO inputs
4. **Fusion**: Evaluates template sensor for confirmed intrusion
5. **Reporting**: Publishes state changes to Home Assistant

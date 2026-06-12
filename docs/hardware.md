# Hardware Reference

## Component Details

### ESP-01 (ESP8266)

| Parameter | Value |
|-----------|-------|
| Processor | Tensilica L106 RISC |
| Clock Speed | 80 MHz (configurable to 160 MHz) |
| Flash | 1 MB |
| WiFi | 802.11 b/g/n |
| GPIO Available | GPIO0, GPIO2 |
| ADC | 1 channel (1V max) |

**Important Notes**:
- GPIO0 is boot-critical: must be pulled HIGH at boot
- GPIO2 is the only usable GPIO for sensors
- Requires 3.3V power supply (NOT 5V)

### HC-SR501 PIR Sensor

| Parameter | Value |
|-----------|-------|
| Power | 4.5V - 20V (typically 5V) |
| Detection Range | Up to 7 meters |
| Detection Angle | ~120 degrees |
| Output | 3.3V logic HIGH when motion detected |
| Delay Adjust | Potentiometer: 0.3s - 20s |
| Sensitivity Adjust | Potentiometer: adjustable |

### RCWL0516 Radar Module

| Parameter | Value |
|-----------|-------|
| Power | 3.3V - 9V (typically 5V) |
| Detection Range | Up to 7 meters (adjustable) |
| Output | 3.3V logic HIGH when motion detected |
| Trigger Mode | One-shot (retriggers on motion) |
| Range Adjust | Potentiometer on board |

## Wiring Guide

### Step-by-Step Wiring

1. **Connect Common Ground**
   - Connect GND of ESP-01, PIR, and Radar together

2. **Power the Sensors**
   - Connect 5V to HC-SR501 VCC
   - Connect 5V to RCWL0516 VCC

3. **Connect ESP-01 Power**
   - Connect 3.3V to ESP-01 VCC
   - Connect GND to ESP-01 GND

4. **Connect PIR Output**
   - HC-SR501 OUT → ESP-01 GPIO2

5. **Connect Radar Output**
   - RCWL0516 OUT → ESP-01 GPIO3 (RX pin)

### Breadboard Layout

```
┌─────────────────────────────────────────────────┐
│                  BREADBOARD                      │
│                                                  │
│  5V Rail ──────► PIR VCC                        │
│  5V Rail ──────► Radar VCC                      │
│  3.3V Rail ────► ESP-01 VCC                     │
│  GND Rail  ────► All GND (all 3 devices)        │
│                                                  │
│  PIR OUT ──────► Jumper ──► GPIO2              │
│  Radar OUT ────► Jumper ──► GPIO3              │
└─────────────────────────────────────────────────┘
```

## Bill of Materials

| Item | Quantity | Estimated Cost |
|------|----------|---------------|
| ESP-01 Module | 1 | $3.00 |
| HC-SR501 PIR Sensor | 1 | $2.50 |
| RCWL0516 Radar Module | 1 | $2.50 |
| Breadboard | 1 | $3.00 |
| Jumper Wires | 1 pack | $2.00 |
| **Total** | | **~$13.00** |

## Optional Enclosure

- IP65-rated outdoor enclosure
- Drill holes for sensor visibility
- Mounting brackets or adhesive strips

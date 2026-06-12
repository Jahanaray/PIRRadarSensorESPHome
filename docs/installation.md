# Installation Guide

## Prerequisites

- ESPHome CLI installed (`pip install esphome`)
- Home Assistant 2023.11+ (recommended)
- USB-TTL adapter (for initial flash, optional if using OTA)
- FTDI/USB-TTL wiring: 3.3V, TX, RX, GND

## Initial Flash (Wired)

### 1. Wire the ESP-01 for Programming

```
USB-TTL ──► ESP-01
───────────────
TX    ──► RX
RX    ──► TX
GND   ──► GND
3.3V  ──► VCC
GPIO0 ──► GND (for flash mode)
```

### 2. Compile the Firmware

```bash
esphome compile SmartPIRRadarV01.yaml
```

Output will be in `.esphome/build/outdoor_security_node_01/`

### 3. Upload to Device

```bash
esphome upload SmartPIRRadarV01.yaml --device /dev/ttyUSB0
```

Replace `/dev/ttyUSB0` with your port (Windows: `COM3`, macOS: `/dev/cu.usbserial-*`)

## OTA Flash (Subsequent Updates)

After initial wired flash:

```bash
# Discover device on network
esphome dashboard

# Or upload directly
esphome upload SmartPIRRadarV01.yaml --device outdoor_security_node_01.local
```

## Configuration Setup

### 1. Generate API Encryption Key

```bash
esphome secret
```

Copy the generated key.

### 2. Edit secrets.yaml

```yaml
wifi_ssid: "your_wifi_ssid"
wifi_password: "your_wifi_password"
api_encryption_key: "pasted_key_here"
ota_username: "admin"
ota_password: "your_ota_password"
fallback_wifi_ssid: "SecurityNode Fallback"
fallback_wifi_password: "your_fallback_password"
```

### 3. Verify Configuration

```bash
esphome config SmartPIRRadarV01.yaml
```

Expected output: `Configuration is valid!`

## Home Assistant Integration

### Automatic (Recommended)

1. Ensure ESPHome Add-on is installed in Home Assistant
2. The device will auto-discover via mDNS
3. Entities appear automatically in Home Assistant

### Manual

1. Go to **Settings > Devices & Services > Add Integration**
2. Select **ESPHome**
3. Enter the IP address or hostname of the device

## Verification

After installation, verify:

1. WiFi connects successfully (check Home Assistant device status)
2. Binary sensors appear in Home Assistant:
   - `binary_sensor.pir_motion`
   - `binary_sensor.radar_motion`
   - `binary_sensor.confirmed_intrusion`
3. Test by triggering each sensor individually
4. Test dual-sensor confirmation

## Troubleshooting Initial Flash

| Issue | Solution |
|-------|----------|
| Device not in flash mode | Ensure GPIO0 is connected to GND during power-on |
| Upload fails | Try shorter USB-TTL cables; reduce baud rate |
| No serial output | Check TX/RX cross-connection (TX→RX, RX→TX) |
| Repeated reboots | Disconnect sensors; test ESP-01 alone first |

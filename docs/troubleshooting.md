# Troubleshooting Guide

## WiFi Issues

### Device Won't Connect to WiFi

**Symptoms**:
- LED blinks rapidly (WiFi scanning)
- Device not visible in Home Assistant

**Solutions**:
1. Verify `secrets.yaml` has correct SSID and password
2. Check WiFi is 2.4GHz (ESP8266 does not support 5GHz)
3. Ensure SSID does not contain special characters
4. Try reducing WiFi transmit power:

```yaml
wifi:
  power_save_mode: none
  transmit_power: 20.5  # dBm (max for ESP8266)
```

### Frequent WiFi Disconnections

**Solutions**:
1. Move device closer to router during testing
2. Check power supply stability (ESP8266 spikes at ~170mA)
3. Add WiFi signal indicator automation:

```yaml
sensor:
  - platform: wifi_signal
    name: "WiFi Signal"
    update_interval: 60s
```

## Sensor Issues

### PIR Sensor Not Detecting

**Symptoms**:
- `pir_motion` never goes to `on`

**Solutions**:
1. Verify wiring: OUT → GPIO2
2. Check pull-up is enabled in configuration
3. Test PIR module separately with a multimeter
4. Allow 60-second warmup period after power-on

### Radar Sensor False Triggers

**Symptoms**:
- `radar_motion` triggers without motion
- `confirmed_intrusion` triggers randomly

**Solutions**:
1. Adjust RCWL0516 sensitivity potentiometer (clockwise to reduce)
2. Increase radar `delayed_on`:

```yaml
- delayed_on: 3000ms  # Increase from 2800ms
```

3. Check for environmental interference (fans, HVAC, vehicles)

### Confirmed Intrusion Never Triggers

**Symptoms**:
- Individual sensors work but `confirmed_intrusion` never activates

**Solutions**:
1. Verify both sensors trigger independently first
2. Check that detection zones overlap physically
3. Reduce confirmation timing:

```yaml
- delayed_on: 100ms  # Reduce from 200ms
```

## Build/Flash Issues

### Compilation Errors

**Common fixes**:
1. Update ESPHome: `pip install --upgrade esphome`
2. Clear build cache: delete `.esphome/` directory
3. Verify YAML syntax: `esphome config SmartPIRRadarV01.yaml`

### Upload Fails

**Common fixes**:
1. Check USB-TTL adapter provides 3.3V (not 5V logic to ESP-01)
2. Hold reset button during upload initiation
3. Ensure GPIO0 is pulled LOW during flash mode
4. Try different USB cable (some are charge-only)

## Home Assistant Issues

### Device Not Discovering

**Solutions**:
1. Verify both devices are on the same subnet
2. Check mDNS is enabled in Home Assistant
3. Try manual integration with IP address

### Entities Not Appearing

**Solutions**:
1. Restart Home Assistant
2. Remove and re-add the ESPHome integration
3. Check API connection in ESPHome dashboard logs

## Performance Issues

### High Memory Usage

The ESP-01 has only 16KB RAM. Monitor usage:

```yaml
# Add to configuration for monitoring
text_sensor:
  - platform: template
    name: "Free Heap"
    update_interval: 60s
    lambda: |-
      return String(ESP.getFreeHeap()).c_str();
```

If memory is critically low, consider:
- Reducing log verbosity
- Removing unnecessary text sensors
- Simplifying lambda expressions

## Reset Procedures

### Factory Reset WiFi Credentials

1. Power on device
2. Keep GPIO0 LOW for 10 seconds
3. Release GPIO0
4. Device creates fallback AP: "SecurityNode Fallback"
5. Connect to AP and reconfigure via captive portal

### Full Restart

Use the `restart_device` switch in Home Assistant, or cycle power.

## Getting Help

When seeking help online, provide:

1. ESPHome version: `esphome --version`
2. Full logs (with `baud_rate: 0` disabled)
3. Sensor states from Home Assistant
4. WiFi signal strength
5. Wiring diagram of your setup

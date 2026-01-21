# Troubleshooting Guide

Common issues and solutions.

## Display Issues

### Display shows nothing
**Solutions:**
- Check 5V power supply (min. 1A)
- Verify Nextion was flashed (.tft file)
- Check UART wiring (TX ↔ RX correct?)
- Test with Nextion Debug Tool

### Display flickers
- Power supply insufficient (use min. 1A)
- Check USB cable quality

## ESP32 Issues

### ESP32 won't start
**Solutions:**
- Check `logger: baud_rate: 0` is set
- Try different UART pins
- Use data cable, not charge-only cable

### "Component not found" error
**Solutions:**
- Check component names (case-sensitive!)
- Verify component exists on current page
- Reflash Nextion completely

## ESPHome Issues

### Sensors show no values
**Solutions:**
- Check `entity_id` is correct
- Verify entity exists in Home Assistant
- Check logs for unavailable sensors

### No Home Assistant connection
**Solutions:**
- Verify WiFi credentials in secrets.yaml
- Check API encryption key
- Restart ESP32
- Check Home Assistant logs

## Nextion Issues

### Touch not working
- Recalibrate touch in Nextion Editor
- Check component IDs match code

### Text not updating
- Verify UART communication
- Check ESPHome logs for set_component_text calls
- Test with simple test command

## Common Mistakes

### ❌ Wrong baud_rate
```yaml
logger:
  baud_rate: 115200  # WRONG - conflicts with Nextion
```

```yaml
logger:
  baud_rate: 0  # CORRECT - frees UART for Nextion
```

### ❌ Wrong entity_id
```yaml
entity_id: sensor.battery  # Template - must change!
```

```yaml
entity_id: sensor.victron_battery_voltage  # Your actual entity
```

## Getting Help

- Check logs: `esphome logs motorhome-display.yaml`
- 🐛 [GitHub Issues](https://github.com/CzarofAK/smartebl_display/issues)
- 💬 [Discussions](https://github.com/CzarofAK/smartebl_display/discussions)
- 📖 [Installation Guide](installation.md)

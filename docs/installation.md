# Installation Guide

Step-by-step instructions for SmartEBL Display.

## Prerequisites

### Hardware
- ESP32 Development Board
- Nextion Display 7" (NX8048P070 Enhanced recommended)
- Jumper cables
- 5V power supply (min. 1A)

### Software
- Nextion Editor
- ESPHome
- Home Assistant (optional)

## Part 1: Hardware Wiring

```
ESP32          Nextion
GPIO17 (TX) -> RX (Yellow)
GPIO16 (RX) <- TX (Blue)
GND         -> GND (Black)
5V          -> +5V (Red)
```

**IMPORTANT:** Use 5V for Nextion, not 3.3V!

## Part 2: Nextion Display

1. Install Nextion Editor
2. Create project (NX8048P070, 800x480, Horizontal)
3. Follow [Page Structure](../nextion/docs/page-structure.md)
4. Compile & flash (.tft file to microSD or USB-Serial)

## Part 3: ESPHome

### Create main config

```
see:
https://github.com/CzarofAK/smartebl/blob/main/secrets.yaml.example
and
https://github.com/CzarofAK/smartebl/blob/main/basic.yaml.example
```

### Create secrets.yaml

```yaml
wifi_ssid: "Your_WiFi"
wifi_password: "Password"
api_encryption_key: "32-char-key"
ota_password: "Password"
```

### Customize entity IDs

Replace all `entity_id` in section files with your entities.

## Part 4: Flash & Test

```bash
# First flash (USB)
esphome run motorhome-display.yaml

# Check logs
esphome logs motorhome-display.yaml
```

## Part 5: Troubleshooting

### Display shows nothing
- Check 5V power
- Check wiring (TX ↔ RX)
- Reflash Nextion

### ESP32 won't start
- Check `logger: baud_rate: 0`
- Try different UART pins
- Change USB cable

### No Home Assistant connection
- Check WiFi credentials
- Check API key
- Restart ESP32

## OTA Updates

After first USB flash, use WiFi:

```bash
esphome run motorhome-display.yaml
# Select: Wirelessly
```

## Next Steps

- [Customization Guide](customization.md)
- [Nextion Page Structure](../nextion/docs/page-structure.md)

Good luck! 🚀

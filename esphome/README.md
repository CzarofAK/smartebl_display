# ESPHome Configuration Files

Modular ESPHome configurations for SmartEBL Display.

## Core Files

- **display-base.yaml** - ESP32 + Nextion UART base
- **display-alarms.yaml** - Master Warning/Caution system

## Sections

Modular sections (mix & match):

- **electric.yaml** - Battery, solar, shore power
- **water.yaml** - Water tanks, pumps
- **climate.yaml** - Heating, climate
- **status.yaml** - System status, fuses, tanks
- **power.yaml** - Energy management

## Quick Start

```yaml
# Your main.yaml
packages:
  display_base: !include esphome/display-base.yaml
  alarms: !include esphome/display-alarms.yaml
  electric: !include esphome/sections/electric.yaml
```

## Customization

Replace all `entity_id` values with your Home Assistant entities:

```yaml
sensor:
  - platform: homeassistant
    id: battery_voltage
    entity_id: sensor.your_battery  # ← CUSTOMIZE
```

## Secrets

Create `secrets.yaml`:

```yaml
wifi_ssid: "Your_WiFi"
wifi_password: "Password"
api_encryption_key: "32-char-key"
ota_password: "OTAPassword"
```

## Testing

```bash
esphome config motorhome-display.yaml
esphome run motorhome-display.yaml
```

See [Installation Guide](../docs/installation.md) for details.

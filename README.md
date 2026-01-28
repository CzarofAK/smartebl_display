# SmartEBL Display

**Professional 7" Nextion Display System for Motorhomes with ESPHome Integration**

Master Warning/Caution system inspired by aviation cockpits - modular, transparent design for complete motorhome monitoring.

---

## Features

- 🚨 **Master Warning/Caution System** - Intelligent alarm prioritization (Red/Orange/Green)
- 📱 **5 Main Sections** - Electric, Tanks, Climate, Status, Power
- 🔍 **Transparent Design** - Minimalist, text-only interface for maximum clarity
- 📊 **Complete Status Overview** - Up to 18 fuses + tank levels + electrical systems
- 🎯 **Intuitive Touch Navigation** - Easy operation while driving
- 🔧 **Modular Architecture** - ESPHome packages for easy mix & match
- 🏠 **Home Assistant Integration** - Real-time data from all sensors
- ⚡ **Package System** - Seamlessly integrates with [SmartEBL](https://github.com/CzarofAK/smartebl)

---

## Quick Start

### 1. Flash Nextion Display

- Download/create `.tft` file from Nextion project
- Load to Nextion via microSD card or USB-serial
- See [nextion.md](nextion/nextion.md) for complete guide

### 2. Configure ESPHome

Create your `motorhome-display.yaml` and include SmartEBL Display packages:

```yaml
# Import SmartEBL base configuration
packages:
  smartebl: github://CzarofAK/smartebl/motorhome.yaml@main

  # Add SmartEBL Display packages
  display_base: github://CzarofAK/smartebl_display/esphome/display-base.yaml@main
  display_alarms: github://CzarofAK/smartebl_display/esphome/display-alarms.yaml@main

  # Add sections you need
  electric: github://CzarofAK/smartebl_display/esphome/sections/electric.yaml@main
  status: github://CzarofAK/smartebl_display/esphome/sections/status.yaml@main
```

### 3. Flash

- build .bin on your ESPhome builder in HA or CLI
- flash the .bin with ESPhome builder in HA or online ESPhome tool

**For detailed installation instructions, see [Installation Guide](docs/installation.md)**

---

## Documentation

### Getting Started
- **[Installation Guide](docs/installation.md)** - Complete step-by-step installation and troubleshooting
- **[Design Guide](docs/design.md)** - Design philosophy, mockups, and layout principles

### Technical Documentation
- **[ESPHome Configuration](esphome/esphome.md)** - Package system, sensors, and customization
- **[Nextion Display Setup](nextion/nextion.md)** - HMI creation, page structure, and components

### Additional Resources
- **[Repository Structure](REPO_STRUCTURE.md)** - File organization overview
- **[Contributing Guidelines](CONTRIBUTING.md)** - How to contribute
- **[License](LICENSE)** - MIT License

---

## System Architecture

```
┌─────────────────────────────────────────────────┐
│                 Home Assistant                  │
│          (Sensors, Entities, Automation)        │
└──────────────────┬──────────────────────────────┘
                   │ WiFi
                   ↓
┌─────────────────────────────────────────────────┐
│              ESP32 (ESPHome)                    │
│  ┌──────────────────────────────────────────┐  │
│  │  SmartEBL Packages                       │  │
│  │  + SmartEBL Display Packages             │  │
│  │  + Alarm Logic                           │  │
│  └──────────────────────────────────────────┘  │
└──────────────────┬──────────────────────────────┘
                   │ UART
                   ↓
┌─────────────────────────────────────────────────┐
│        7" Nextion Display (800x480)             │
│   Master Warning | Electric | Tanks | Climate   │
│         Status   |   Power  |  Navigation       │
└─────────────────────────────────────────────────┘
```

---

## Integration with SmartEBL

This display system is designed to work seamlessly with **[SmartEBL](https://github.com/CzarofAK/smartebl)** - a complete ESPHome-based motorhome control system.

### Combined Setup

1. **SmartEBL** provides all sensor configurations and Home Assistant integration
2. **SmartEBL Display** adds the visual interface and alarm system
3. Both use the same ESPHome packages architecture for easy integration

### Example Combined Configuration

```yaml
# motorhome-display.yaml

substitutions:
  device_name: motorhome-display

packages:
  # SmartEBL base system (sensors, relays, automation)
  smartebl: github://CzarofAK/smartebl/motorhome.yaml@main

  # SmartEBL Display (Nextion interface + alarms)
  display_base: github://CzarofAK/smartebl_display/esphome/display-base.yaml@main
  display_alarms: github://CzarofAK/smartebl_display/esphome/display-alarms.yaml@main
  electric: github://CzarofAK/smartebl_display/esphome/sections/electric.yaml@main
  status: github://CzarofAK/smartebl_display/esphome/sections/status.yaml@main

# Your custom additions
sensor:
  # Add custom sensors here
```

See [ESPHome documentation](esphome/esphome.md) for complete package system guide.

---

## Display Sections

| Section | Pages | Content |
|---------|-------|---------|
| **Electric** | 2 | Battery voltage/current/SOC, Solar power, Shore power, Alternator |
| **Tanks** | 2 | All fluid levels (fresh/waste water, diesel, AdBlue, gas bottles), Pump control |
| **Climate** | 2 | Interior/exterior temperature, Humidity, Heater status |
| **Status** | 2 | 18 Fuses overview, Tank levels, System status |
| **Power** | 2 | Energy consumption, Power sources, Inverter |

---

## Screenshots

<!-- Add actual screenshots here once available -->

### Home Screen
```
┌─────────────────────────────────────────────────┐
│  ✓ ALL SYSTEMS NORMAL                          │
├───────────┬─────────────────────────────────────┤
│           │                                     │
│ Electric  │        MAIN MENU                    │
│ Tanks     │   SmartEBL Display System           │
│ Climate   │  Select a section from the menu     │
│ Status    │                                     │
│ Power     │                                     │
└───────────┴─────────────────────────────────────┘
```

See [Design Guide](docs/design.md) for complete mockups.

---

## Requirements

### Hardware
- **ESP32** Development Board (ESP32-WROOM-32 or similar)
- **Nextion Display** 7" Enhanced (NX8048P070 recommended)
- **Power Supply** 5V/1A minimum
- **Cables** Jumper wires for UART connection

### Software
- **ESPHome** (latest version)
- **Home Assistant** (recommended for sensor integration)
- **Nextion Editor** (for HMI customization)

---

## Related Projects

- **[SmartEBL](https://github.com/CzarofAK/smartebl)** - Complete ESPHome motorhome control system
- **[ESPHome](https://esphome.io/)** - Platform for ESP32/ESP8266 firmware
- **[Home Assistant](https://www.home-assistant.io/)** - Open-source home automation

---

## Support & Community

- 📖 **[Documentation](docs/)** - Complete guides
- 🐛 **[Issues](https://github.com/CzarofAK/smartebl_display/issues)** - Bug reports & feature requests
- 💬 **[Discussions](https://github.com/CzarofAK/smartebl_display/discussions)** - Community support
- 🤝 **[Contributing](CONTRIBUTING.md)** - Contribution guidelines

---

## License

MIT License - See [LICENSE](LICENSE) for details.

---

## Credits

Developed by [CzarofAK](https://github.com/CzarofAK)

Inspired by aviation cockpit systems (Boeing/Airbus Master Warning/Caution displays).

---

## Version

**Version:** 1.0.0
**Status:** Production Ready
**Last Updated:** 2025-01-27

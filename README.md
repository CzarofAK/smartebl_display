# SmartEBL Display

**Modular 7" Nextion Display System for Motorhomes with ESPHome Integration**

Professional Master Warning/Caution System inspired by aviation cockpits.

## Features

- 🚨 Master Warning/Caution System - Intelligent alarm prioritization
- 📱 5 Main Sections - Electric, Water, Climate, Status, Power
- 🔍 Transparent Design - Minimalist, text-only visibility
- 📊 Status Overview - Up to 18 fuses + tank levels
- 🎯 Touch Navigation - Intuitive operation
- 🔧 Modular - ESPHome packages for easy mix & match

## Quick Start

### 1. Nextion Display
See [nextion/docs/page-structure.md](nextion/docs/page-structure.md)

### 2. ESPHome
```yaml
packages:
  display_base: !include esphome/display-base.yaml
  alarms: !include esphome/display-alarms.yaml
  electric: !include esphome/sections/electric.yaml
  status: !include esphome/sections/status.yaml
```

### 3. Flash

- build .bin on your ESPhome builder in HA or CLI
- flash the .bin with ESPhome builder in HA or online ESPhome tool

## Documentation

- [Installation Guide](docs/installation.md)
- [Nextion Page Structure](nextion/docs/page-structure.md)
- [ESPHome Sections](esphome/README.md)

## License

MIT License - See [LICENSE](LICENSE)

## Credits

Developed by [CzarofAK](https://github.com/CzarofAK)

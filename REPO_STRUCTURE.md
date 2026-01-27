# SmartEBL Display - Repository Structure

**Complete Overview of All Files and Directories**

---

## NEW: Consolidated Documentation Structure

All documentation has been reorganized into **5 main comprehensive guides**:

```
smartebl_display/
│
├── README.md                          # Main overview + quick start
│
├── docs/                              # User Guides
│   ├── installation.md                # Complete installation + troubleshooting
│   └── design.md                      # Design philosophy + mockups + layout
│
├── esphome/                           # ESPHome Configuration
│   └── esphome.md                     # Package system + customization guide
│
└── nextion/                           # Nextion Display
    └── nextion.md                     # HMI creation + page structure + components
```

**Benefits:**
- ✅ Easier to find information (5 files instead of 10+)
- ✅ Better flow and structure
- ✅ Less duplication
- ✅ Single source of truth for each topic

---

## Complete Directory Structure

```
smartebl_display/
│
├── README.md                          # 📖 Main documentation + quick start
├── LICENSE                            # MIT License
├── CONTRIBUTING.md                    # Contribution guidelines
├── REPO_STRUCTURE.md                  # This file
├── .gitignore                         # Git ignore rules
│
├── docs/                              # 📚 User Documentation
│   ├── installation.md                # ✅ Complete installation + troubleshooting
│   ├── design.md                      # ✅ Design philosophy + mockups + layout
│   ├── customization.md               # 🗑️ DEPRECATED (merged into esphome.md)
│   └── troubleshooting.md             # 🗑️ DEPRECATED (merged into installation.md)
│
├── nextion/                           # 🖥️ Nextion Display Files
│   ├── nextion.md                     # ✅ NEW: Complete Nextion guide
│   ├── docs/                          # 🗑️ Legacy documentation (deprecated)
│   │   ├── page-structure.md          # 🗑️ DEPRECATED (merged into nextion.md)
│   │   ├── design-guide.md            # 🗑️ DEPRECATED (merged into design.md)
│   │   └── component-reference.md     # 🗑️ DEPRECATED (merged into nextion.md)
│   ├── screenshots/
│   │   └── MOCKUPS.md                 # 🗑️ DEPRECATED (merged into design.md)
│   ├── examples/
│   │   └── README.md                  # Placeholder for community examples
│   └── README.md                      # Quick Nextion overview
│
└── esphome/                           # ⚡ ESPHome Configurations
    ├── esphome.md                     # ✅ NEW: Complete ESPHome guide
    ├── display-base.yaml              # ✅ CORE: ESP32 + Nextion UART
    ├── display-alarms.yaml            # ✅ CORE: Master Warning/Caution system
    ├── sections/                      # Modular display sections
    │   ├── electric.yaml              # ✅ Electric section (battery, solar, shore)
    │   ├── water.yaml                 # ✅ Water section (tanks, pumps)
    │   ├── climate.yaml               # ✅ Climate section (temp, heating)
    │   ├── status.yaml                # ✅ Status section (fuses, overview)
    │   └── power.yaml                 # ✅ Power section (energy management)
    ├── examples/
    │   └── full-config.yaml           # ✅ Complete example configuration
    └── README.md                      # Quick ESPHome overview
```

---

## File Status & Purpose

### ✅ Production Ready - Core Documentation

| File | Status | Purpose |
|------|--------|---------|
| `README.md` | ✅ Complete | Main overview, features, quick start, SmartEBL integration |
| `docs/installation.md` | ✅ Complete | Complete installation + troubleshooting (consolidated) |
| `docs/design.md` | ✅ Complete | Design philosophy + mockups + layout (consolidated) |
| `esphome/esphome.md` | ✅ Complete | Package system + customization + integration guide |
| `nextion/nextion.md` | ✅ Complete | HMI creation + page structure + components (consolidated) |

### ✅ Production Ready - Configuration Files

| File | Status | Purpose |
|------|--------|---------|
| `esphome/display-base.yaml` | ✅ Complete | ESP32 + UART base configuration |
| `esphome/display-alarms.yaml` | ✅ Complete | Master Warning/Caution system |
| `esphome/sections/electric.yaml` | ✅ Complete | Electric section (battery, solar, shore power) |
| `esphome/sections/status.yaml` | ✅ Complete | Status section (fuses, tanks) |
| `esphome/sections/water.yaml` | ✅ Complete | Water section template |
| `esphome/sections/climate.yaml` | ✅ Complete | Climate section template |
| `esphome/sections/power.yaml` | ✅ Complete | Power section template |
| `esphome/examples/full-config.yaml` | ✅ Complete | Complete example configuration |

### 🗑️ Deprecated Files (To Be Removed)

These files have been **consolidated** into the main documentation:

| Old File | Merged Into | Status |
|----------|-------------|--------|
| `docs/customization.md` | `esphome/esphome.md` | 🗑️ Can be removed |
| `docs/troubleshooting.md` | `docs/installation.md` | 🗑️ Can be removed |
| `nextion/docs/page-structure.md` | `nextion/nextion.md` | 🗑️ Can be removed |
| `nextion/docs/component-reference.md` | `nextion/nextion.md` | 🗑️ Can be removed |
| `nextion/docs/design-guide.md` | `docs/design.md` | 🗑️ Can be removed |
| `nextion/screenshots/MOCKUPS.md` | `docs/design.md` | 🗑️ Can be removed |

---

## Quick Navigation Guide

### For New Users (Start Here)

1. **README.md** - Overview, features, quick start
2. **docs/installation.md** - Complete installation guide
3. **nextion/nextion.md** - Create Nextion HMI
4. **esphome/esphome.md** - Configure ESPHome packages

### For Customization

**Nextion Display:**
- **nextion/nextion.md** - Page structure, components, navigation
- **docs/design.md** - Design principles, mockups, colors, layout

**ESPHome Configuration:**
- **esphome/esphome.md** - Package system, add sensors, customize sections
- **esphome/sections/*.yaml** - Modify individual section configurations

### For Troubleshooting

- **docs/installation.md** → Section: "Troubleshooting"
- Check ESPHome logs: `esphome logs motorhome-display.yaml`
- **GitHub Issues**: [Report problems](https://github.com/CzarofAK/smartebl_display/issues)

---

## Core Files (Critical - Do Not Modify)

- `esphome/display-base.yaml` - UART & Display base
- `esphome/display-alarms.yaml` - Alarm logic
- Core documentation files (README, installation, etc.)

**Changes only via Pull Request!**

---

## User-Customizable Files

- `esphome/sections/*.yaml` - Customize individual sections
- Your own `motorhome-display.yaml` - Main configuration
- Your own `secrets.yaml` - WiFi & API credentials (not in repo!)

---

## GitHub Workflow

```bash
# Clone repository
git clone https://github.com/CzarofAK/smartebl_display.git

# Create your own config
cd smartebl_display/esphome
cp examples/full-config.yaml ../motorhome-display.yaml

# Create secrets
cat > secrets.yaml << EOF
wifi_ssid: "Your_WiFi"
wifi_password: "Password"
api_encryption_key: "32-Character-Key"
ota_password: "OTAPassword"
EOF

# Flash ESPHome
cd ..
esphome run motorhome-display.yaml
```

---

## Important Notes

⚠️ **Nextion .HMI Files**
- CANNOT be version controlled (binary)
- Use `nextion/docs/page-structure.md` as guide
- Create regular local backups

⚠️ **Secrets**
- `secrets.yaml` is in `.gitignore`
- Never commit passwords!

⚠️ **Entity IDs**
- All `entity_id` in Sections are placeholders
- **MUST** be replaced with your own Home Assistant Entities

---

## Support & Community

- 📖 [Documentation](docs/)
- 🐛 [Issues](https://github.com/CzarofAK/smartebl_display/issues)
- 💬 [Discussions](https://github.com/CzarofAK/smartebl_display/discussions)
- 🤝 [Contributing](CONTRIBUTING.md)

---

**Repository Created:** 2025-01-21
**Version:** 1.0.0
**Status:** Production Ready

All core features implemented and documented!

# SmartEBL Display - Repository Structure

**Complete Overview of All Files and Directories**

```
smartebl_display/
│
├── README.md                       # Main README with overview
├── LICENSE                         # MIT License
├── CONTRIBUTING.md                 # Contribution Guidelines
├── .gitignore                      # Git Ignore Rules
│
├── docs/                           # General Documentation
│   ├── installation.md             # Step-by-Step Installation
│   └── customization.md            # Customization Guide
│
├── nextion/                        # Nextion Display Files
│   ├── README.md                   # Nextion Overview
│   ├── docs/                       # Nextion Documentation
│   │   ├── page-structure.md       # Complete Page Structure (IMPORTANT!)
│   │   ├── design-guide.md         # Design Principles
│   │   └── component-reference.md  # Component Reference
│   ├── screenshots/                # Display Mockups
│   │   └── MOCKUPS.md              # ASCII-Art Visualizations
│   └── examples/                   # Example Projects
│       └── README.md               # Placeholder
│
└── esphome/                        # ESPHome Configurations
    ├── README.md                   # ESPHome Overview
    ├── display-base.yaml           # Base: ESP32 + UART (CORE)
    ├── display-alarms.yaml         # Master Warning/Caution System (CORE)
    ├── sections/                   # Modular Sections
    │   ├── electric.yaml           # Electric Section (Implemented)
    │   ├── water.yaml              # Water Section (Template)
    │   ├── climate.yaml            # Climate Section (Template)
    │   ├── status.yaml             # Status Section (Implemented)
    │   └── power.yaml              # Power Section (Template)
    └── examples/
        └── full-config.yaml        # Complete Example
```

---

## File Status

### ✅ Complete & Ready to Use

- `README.md` - Main Documentation
- `LICENSE` - MIT License
- `CONTRIBUTING.md` - Contribution Guide
- `.gitignore` - Git Ignore
- `docs/installation.md` - Installation Guide
- `docs/customization.md` - Customization Guide
- `nextion/docs/page-structure.md` - **CRITICAL: Complete Nextion Guide**
- `nextion/docs/design-guide.md` - Design Best Practices
- `nextion/docs/component-reference.md` - Component Reference
- `nextion/screenshots/MOCKUPS.md` - Display Mockups
- `esphome/display-base.yaml` - **CORE: ESP32 Base**
- `esphome/display-alarms.yaml` - **CORE: Alarm System**
- `esphome/sections/electric.yaml` - Electric Section (complete)
- `esphome/sections/status.yaml` - Status Section (complete)
- `esphome/examples/full-config.yaml` - Example Config

### 📝 Templates (Customization Required)

- `esphome/sections/water.yaml` - Template for Water
- `esphome/sections/climate.yaml` - Template for Climate
- `esphome/sections/power.yaml` - Template for Power

---

## Quick Start Sequence

**For New Users:**

1. **Read README.md** → Overview
2. **docs/installation.md** → Step-by-Step Installation
3. **nextion/docs/page-structure.md** → Create Nextion Display
4. **esphome/display-base.yaml** → Configure ESP32
5. **esphome/sections/electric.yaml** → Customize First Section
6. **docs/customization.md** → Your Own Customizations

---

## Core Files (Do Not Modify Without Good Reason)

- `esphome/display-base.yaml` - UART & Display Base
- `esphome/display-alarms.yaml` - Alarm Logic
- `nextion/docs/page-structure.md` - Nextion Reference

**Changes Only Via Pull Request!**

---

## User-Customizable Files

- `esphome/sections/*.yaml` - Customize sections
- `esphome/examples/full-config.yaml` - Use as template
- Own Secrets: `secrets.yaml` (not in repo!)

---

## Missing Files (Optional, Add Later)

- [ ] `docs/troubleshooting.md` - Troubleshooting Guide
- [ ] `nextion/*.HMI` - Nextion Project Files (cannot be version controlled!)
- [ ] Community examples in `nextion/examples/`
- [ ] Screenshots in `nextion/screenshots/` (actual images)

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

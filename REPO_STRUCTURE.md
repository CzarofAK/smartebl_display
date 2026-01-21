# SmartEBL Display - Repository Structure

**Vollständige Übersicht aller Files und Verzeichnisse**

```
smartebl_display/
│
├── README.md                       # Haupt-README mit Übersicht
├── LICENSE                         # MIT License
├── CONTRIBUTING.md                 # Contribution Guidelines
├── .gitignore                      # Git Ignore Regeln
│
├── docs/                           # Allgemeine Dokumentation
│   ├── installation.md             # Installation Schritt-für-Schritt
│   └── customization.md            # Anpassungs-Guide
│
├── nextion/                        # Nextion Display Files
│   ├── README.md                   # Nextion Übersicht
│   ├── docs/                       # Nextion Dokumentation
│   │   ├── page-structure.md       # Komplette Seitenstruktur (WICHTIG!)
│   │   ├── design-guide.md         # Design-Prinzipien
│   │   └── component-reference.md  # Komponenten-Referenz
│   ├── screenshots/                # Display Mockups
│   │   └── MOCKUPS.md              # ASCII-Art Visualisierungen
│   └── examples/                   # Beispiel-Projekte
│       └── README.md               # Platzhalter
│
└── esphome/                        # ESPHome Konfigurationen
    ├── README.md                   # ESPHome Übersicht
    ├── display-base.yaml           # Basis: ESP32 + UART (CORE)
    ├── display-alarms.yaml         # Master Warning/Caution System (CORE)
    ├── sections/                   # Modulare Sektionen
    │   ├── electric.yaml           # Electric Sektion (Implementiert)
    │   ├── water.yaml              # Water Sektion (Template)
    │   ├── climate.yaml            # Climate Sektion (Template)
    │   ├── status.yaml             # Status Sektion (Implementiert)
    │   └── power.yaml              # Power Sektion (Template)
    └── examples/
        └── full-config.yaml        # Vollständiges Beispiel
```

---

## File Status

### ✅ Komplett & Einsatzbereit

- `README.md` - Haupt-Dokumentation
- `LICENSE` - MIT License
- `CONTRIBUTING.md` - Contribution Guide
- `.gitignore` - Git Ignore
- `docs/installation.md` - Installation Guide
- `docs/customization.md` - Customization Guide
- `nextion/docs/page-structure.md` - **KRITISCH: Komplette Nextion Anleitung**
- `nextion/docs/design-guide.md` - Design Best Practices
- `nextion/docs/component-reference.md` - Komponenten-Referenz
- `nextion/screenshots/MOCKUPS.md` - Display Mockups
- `esphome/display-base.yaml` - **CORE: ESP32 Basis**
- `esphome/display-alarms.yaml` - **CORE: Alarm-System**
- `esphome/sections/electric.yaml` - Electric Sektion (vollständig)
- `esphome/sections/status.yaml` - Status Sektion (vollständig)
- `esphome/examples/full-config.yaml` - Beispiel-Config

### 📝 Templates (Anpassung erforderlich)

- `esphome/sections/water.yaml` - Template für Water
- `esphome/sections/climate.yaml` - Template für Climate
- `esphome/sections/power.yaml` - Template für Power

---

## Quick Start Reihenfolge

**Für neue User:**

1. **README.md lesen** → Übersicht
2. **docs/installation.md** → Installation Schritt-für-Schritt
3. **nextion/docs/page-structure.md** → Nextion Display erstellen
4. **esphome/display-base.yaml** → ESP32 konfigurieren
5. **esphome/sections/electric.yaml** → Erste Sektion anpassen
6. **docs/customization.md** → Eigene Anpassungen

---

## Core Files (Nicht ändern ohne Grund)

- `esphome/display-base.yaml` - UART & Display Basis
- `esphome/display-alarms.yaml` - Alarm-Logik
- `nextion/docs/page-structure.md` - Nextion Referenz

**Änderungen nur via Pull Request!**

---

## User-Anpassbare Files

- `esphome/sections/*.yaml` - Sektionen anpassen
- `esphome/examples/full-config.yaml` - Als Template nutzen
- Eigene Secrets: `secrets.yaml` (nicht im Repo!)

---

## Fehlende Files (Optional, später hinzufügen)

- [ ] `docs/troubleshooting.md` - Troubleshooting Guide
- [ ] `nextion/*.HMI` - Nextion Projekt-Files (können nicht versioniert werden!)
- [ ] Community-Beispiele in `nextion/examples/`
- [ ] Screenshots in `nextion/screenshots/` (echte Bilder)

---

## GitHub Workflow

```bash
# Repo klonen
git clone https://github.com/CzarofAK/smartebl_display.git

# Eigene Config erstellen
cd smartebl_display/esphome
cp examples/full-config.yaml ../motorhome-display.yaml

# Secrets erstellen
cat > secrets.yaml << EOF
wifi_ssid: "Dein_WiFi"
wifi_password: "Passwort"
api_encryption_key: "32-Zeichen-Key"
ota_password: "OTAPasswort"
EOF

# ESPHome flashen
cd ..
esphome run motorhome-display.yaml
```

---

## Wichtige Hinweise

⚠️ **Nextion .HMI Files**
- Können NICHT versioniert werden (binär)
- Nutze `nextion/docs/page-structure.md` als Anleitung
- Erstelle regelmäßig lokale Backups

⚠️ **Secrets**
- `secrets.yaml` ist in `.gitignore`
- Nie Passwörter committen!

⚠️ **Entity IDs**
- Alle `entity_id` in Sections sind Platzhalter
- **MÜSSEN** durch eigene Home Assistant Entities ersetzt werden

---

## Support & Community

- 📖 [Dokumentation](docs/)
- 🐛 [Issues](https://github.com/CzarofAK/smartebl_display/issues)
- 💬 [Discussions](https://github.com/CzarofAK/smartebl_display/discussions)
- 🤝 [Contributing](CONTRIBUTING.md)

---

**Repository erstellt am:** 2025-01-21
**Version:** 1.0.0
**Status:** Production Ready

Alle Core-Funktionen implementiert und dokumentiert!

# SmartEBL Display

**Modulares 7" Nextion Display System für Wohnmobile mit ESPHome Integration**

Professionelles Master Warning/Caution System inspiriert von Luftfahrt-Cockpits.

![Version](https://img.shields.io/badge/version-1.0.0-blue)
![Nextion](https://img.shields.io/badge/Nextion-7%22%20Display-orange)
![ESPHome](https://img.shields.io/badge/ESPHome-2024.x-purple)
![License](https://img.shields.io/badge/license-MIT-green)

---

## ✨ Features

- 🚨 **Master Warning/Caution System** - Intelligente Alarm-Priorisierung
- 📱 **5 Hauptsektionen** - Electric, Water, Climate, Status, Power
- 🔍 **Transparentes Design** - Minimalistisch, nur Text sichtbar
- 📊 **Status-Übersicht** - Bis zu 18 Sicherungen + Tank-Level + mehr
- 🎯 **Touch-Navigation** - Intuitive Bedienung mit Seiten-Indikatoren
- 🔧 **Modular** - ESPHome Packages zum einfachen Mix & Match

---

## 📁 Repo-Struktur

```
smartebl_display/
├── nextion/                    # Nextion Display Files
│   ├── docs/                   # Nextion-spezifische Dokumentation
│   │   ├── page-structure.md   # Komplette Seitenstruktur
│   │   ├── design-guide.md     # Design-Prinzipien
│   │   └── component-reference.md
│   ├── screenshots/            # Display Mockups
│   └── examples/               # Beispiel-Projekte
│
├── esphome/                    # ESPHome Konfigurationen
│   ├── display-base.yaml       # Basis: ESP32 + UART
│   ├── display-alarms.yaml     # Master Warning/Caution Logik
│   ├── sections/               # Modulare Sektionen
│   │   ├── electric.yaml
│   │   ├── water.yaml
│   │   ├── climate.yaml
│   │   ├── status.yaml
│   │   └── power.yaml
│   └── examples/
│       └── full-config.yaml    # Komplettes Beispiel
│
├── docs/                       # Allgemeine Dokumentation
│   ├── installation.md
│   ├── customization.md
│   └── troubleshooting.md
│
├── README.md                   # Diese Datei
└── LICENSE
```

---

## 🚀 Quick Start

### 1️⃣ Nextion Display vorbereiten

Siehe [`nextion/docs/page-structure.md`](nextion/docs/page-structure.md)

1. Nextion Editor öffnen
2. Neues Projekt erstellen (NX8048P070, 800x480)
3. Anleitung folgen
4. Auf Display flashen

### 2️⃣ ESPHome konfigurieren

```yaml
# Deine main.yaml
substitutions:
  device_name: motorhome-display
  
packages:
  # Basis importieren
  display_base: github://CzarofAK/smartebl_display/esphome/display-base.yaml@main
  alarms: github://CzarofAK/smartebl_display/esphome/display-alarms.yaml@main
  
  # Nur benötigte Sektionen importieren
  electric: github://CzarofAK/smartebl_display/esphome/sections/electric.yaml@main
  water: github://CzarofAK/smartebl_display/esphome/sections/water.yaml@main
  status: github://CzarofAK/smartebl_display/esphome/sections/status.yaml@main

# Eigene Home Assistant Entities eintragen
binary_sensor:
  - platform: homeassistant
    id: sicherung_1
    entity_id: binary_sensor.fuse_main  # ← ANPASSEN
```

### 3️⃣ Flashen & Genießen

```bash
esphome run motorhome-display.yaml
```

---

## 🎨 Display Layout

### Home Screen
```
┌─────────────────────────────────────────────────────────┐
│ ⚠️ MASTER WARNING                                       │
├───────────┬─────────────────────────────────────────────┤
│           │                                             │
│ Electric  │                                             │
│           │                                             │
│ Water     │         HAUPTMENÜ                           │
│           │                                             │
│ Climate   │                                             │
│           │                                             │
│ Status    │                                             │
│           │                                             │
│ Power     │                                             │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

### Unterseiten (z.B. Electric_1)
```
┌─────────────────────────────────────────────────────────┐
│ ✓ ALL SYSTEMS NORMAL                           1/2      │
├───────────┬─────────────────────────────────────────────┤
│           │                                             │
│ Electric  │  BATTERY STATUS                             │
│ ━━━━━━    │  Voltage: 12.8V                             │
│ Water     │  Current: 5.2A                              │
│           │  SOC: 78%                                   │
│ Climate   │                                             │
│           │  SOLAR                                      │
│ Status    │  Power: 245W                                │
│           │                                             │
│ Power     │                          [Next Page →]      │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

---

## 📊 Master Warning/Caution System

### Alarm-Hierarchie

| Priorität | Name | Farbe | Beispiele |
|-----------|------|-------|-----------|
| **HIGH** | Master WARNING | 🔴 Rot | Batterie kritisch < 20%, Gas leer, Hauptsicherung |
| **MEDIUM** | Master CAUTION | 🟠 Orange | Batterie niedrig < 40%, Tank < 20%, Sicherung defekt |
| **NORMAL** | All Systems Normal | 🟢 Grün | Keine aktiven Alarme |

### Lauftext-Anzeige

```
Keine Alarme:      ✓ ALL SYSTEMS NORMAL
1-5 CAUTION:       ⚡ MASTER CAUTION
1+ WARNING:        ⚠️ MASTER WARNING
```

**Details immer auf Status-Page sichtbar!**

---

## 🔧 Modulare Sektionen

Importiere nur was du brauchst:

```yaml
packages:
  # Minimal Setup (nur Status)
  display_base: !include esphome/display-base.yaml
  alarms: !include esphome/display-alarms.yaml
  status: !include esphome/sections/status.yaml

  # Erweitert (Electric + Water)
  electric: !include esphome/sections/electric.yaml
  water: !include esphome/sections/water.yaml
```

Jede Sektion ist **unabhängig** und kann einzeln aktiviert werden.

---

## 📚 Dokumentation

### Nextion
- [Page Structure](nextion/docs/page-structure.md) - Komplette Seitenstruktur
- [Design Guide](nextion/docs/design-guide.md) - Design-Prinzipien
- [Component Reference](nextion/docs/component-reference.md) - Alle Komponenten

### ESPHome
- [Installation Guide](docs/installation.md) - Schritt-für-Schritt
- [Customization](docs/customization.md) - Anpassungen
- [Troubleshooting](docs/troubleshooting.md) - Häufige Probleme

---

## 🎯 Anpassung

### Eigene Sensoren hinzufügen

```yaml
# In deiner main.yaml
binary_sensor:
  - platform: homeassistant
    id: sicherung_18
    entity_id: binary_sensor.your_fuse_18  # ← HIER
    on_press:
      - script.execute: check_alarms  # Triggert Alarm-Check
```

### Neue Sektion erstellen

1. Kopiere `esphome/sections/electric.yaml`
2. Benenne um zu `lighting.yaml`
3. Passe Sensoren an
4. Importiere in main.yaml

Siehe [`docs/customization.md`](docs/customization.md) für Details.

---

## 🤝 Contributing

Beiträge willkommen! Bitte:

1. Fork das Repo
2. Feature Branch erstellen
3. Commit mit klaren Messages
4. Pull Request öffnen

---

## 📝 License

MIT License - Siehe [LICENSE](LICENSE) für Details.

---

## 🙏 Credits

Entwickelt von [CzarofAK](https://github.com/CzarofAK) für die SmartEBL Community.

Inspiriert von professionellen Luftfahrt-Cockpit-Systemen.

---

## 📧 Support

- 🐛 Issues: [GitHub Issues](https://github.com/CzarofAK/smartebl_display/issues)
- 💬 Diskussionen: [GitHub Discussions](https://github.com/CzarofAK/smartebl_display/discussions)
- 📖 Wiki: [GitHub Wiki](https://github.com/CzarofAK/smartebl_display/wiki)

---

**⭐ Wenn dir dieses Projekt hilft, gib ihm einen Star!**

# Nextion Component Reference

**Übersicht aller verwendeten Nextion-Komponenten im SmartEBL Display**

---

## Standard-Komponenten (auf jeder Page)

### txt_alert - Lauftext (Master Warning/Caution)

| Eigenschaft | Wert | Beschreibung |
|-------------|------|--------------|
| **Typ** | Scrolling Text | Lauftext mit automatischem Scroll |
| **Name** | txt_alert | Eindeutiger Name |
| **Position** | x=0, y=0 | Oben links |
| **Größe** | w=800, h=50 | Volle Breite |
| **Font** | Font 3 (24pt) | Gut lesbar |
| **bco** | 65535 | Transparent |
| **pco** | 2016 (Grün) | Text-Farbe (dynamisch) |
| **pco2** | 65535 | Transparent |
| **dir** | 0 | Left-to-right |
| **dis** | 1 | Scrolling aktiviert |
| **tim** | 50 | Scroll-Geschwindigkeit |
| **xcen** | 1 | Horizontal zentriert |
| **ycen** | 1 | Vertikal zentriert |

**ESPHome Update:**
```cpp
id(nextion_display).set_component_text("txt_alert", "⚠️ MASTER WARNING");
id(nextion_display).set_component_value("txt_alert.pco", 63488);  // Rot
```

---

### txt_page_counter - Seitenzähler

| Eigenschaft | Wert | Beschreibung |
|-------------|------|--------------|
| **Typ** | Text | Statischer Text |
| **Name** | txt_page_counter | Eindeutiger Name |
| **Position** | x=700, y=10 | Rechts oben |
| **Größe** | w=80, h=30 | Kompakt |
| **Font** | Font 1 (16pt) | Standard |
| **bco** | 65535 | Transparent |
| **pco** | 65535 | Weiß |
| **txt** | "1/2" | Beispiel-Text |
| **xcen** | 2 | Rechts ausgerichtet |
| **ycen** | 1 | Vertikal zentriert |

**Nextion Update (Page Preinit):**
```c
txt_page_counter.txt="1/2"
```

---

### Menu Buttons (5x)

#### btn_electric, btn_water, btn_climate, btn_status, btn_power

| Eigenschaft | Wert | Beschreibung |
|-------------|------|--------------|
| **Typ** | Button | Touch-Button |
| **Position** | x=0, y=60/150/240/330/420 | Vertikal gestaffelt |
| **Größe** | w=150, h=80 | Touch-freundlich |
| **Font** | Font 2 (20pt) | Gut lesbar |
| **bco** | 65535 | Transparent |
| **pco** | 65535 | Weiß (Standard) |
| **pco2** | 65535 | Transparent |
| **txt** | "Electric", "Water", etc. | Button-Text |

**Touch Release Event (Beispiel btn_electric auf Home):**
```c
currentSection=1
currentPage=0
page Electric_1
```

**Page Preinit (Highlighting aktiver Button):**
```c
btn_electric.pco=2016  // Grün = Aktiv
btn_water.pco=65535    // Weiß = Inaktiv
```

---

## Electric Section Komponenten

### bat_volt - Batterie Spannung

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Name** | bat_volt |
| **Page** | Electric_1 |
| **Font** | Font 2 (20pt) |
| **bco** | 65535 (transparent) |
| **pco** | 65535 (weiß) |

**ESPHome:**
```cpp
char buffer[10];
snprintf(buffer, sizeof(buffer), "%.1fV", voltage);
id(nextion_display).set_component_text("bat_volt", buffer);
```

### bat_current - Batterie Strom

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Name** | bat_current |
| **Page** | Electric_1 |
| **Font** | Font 2 (20pt) |

**ESPHome:**
```cpp
snprintf(buffer, sizeof(buffer), "%.1fA", current);
id(nextion_display).set_component_text("bat_current", buffer);
```

### bat_soc - Batterie State of Charge

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Name** | bat_soc |
| **Page** | Electric_1 |
| **Font** | Font 4 (32pt) |
| **pco** | Dynamisch (Grün/Orange/Rot) |

**ESPHome:**
```cpp
snprintf(buffer, sizeof(buffer), "%.0f%%", soc);
id(nextion_display).set_component_text("bat_soc", buffer);

// Farbe basierend auf SOC
int color = (soc < 20) ? 63488 : (soc < 40) ? 64512 : 2016;
id(nextion_display).set_component_value("bat_soc.pco", color);
```

### solar_power - Solar Leistung

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Name** | solar_power |
| **Page** | Electric_1 |

**ESPHome:**
```cpp
snprintf(buffer, sizeof(buffer), "%.0fW", power);
id(nextion_display).set_component_text("solar_power", buffer);
```

### shore_status - Shore Power Status

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Name** | shore_status |
| **Page** | Electric_1 |
| **pco** | Dynamisch (Grün=Connected, Grau=Disconnected) |

**ESPHome:**
```cpp
if (connected) {
  id(nextion_display).set_component_text("shore_status", "Connected");
  id(nextion_display).set_component_value("shore_status.pco", 2016);
} else {
  id(nextion_display).set_component_text("shore_status", "Disconnected");
  id(nextion_display).set_component_value("shore_status.pco", 50712);
}
```

---

## Status Section Komponenten

### sich_1 bis sich_18 - Sicherungen

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Name** | sich_1, sich_2, ..., sich_18 |
| **Page** | Status_1 |
| **Layout** | Grid 3x6 |
| **Font** | Font 1 (16pt) |
| **pco** | Dynamisch (Grün=OK, Rot=Defekt) |

**Position (Beispiel):**
```
sich_1:  x=170, y=70,  w=180, h=40
sich_2:  x=370, y=70,  w=180, h=40
sich_3:  x=570, y=70,  w=180, h=40
sich_4:  x=170, y=120, w=180, h=40
...
```

**ESPHome:**
```cpp
// Für jede Sicherung
int color = defekt ? 63488 : 2016;  // Rot : Grün
id(nextion_display).set_component_value("sich_1.pco", color);
```

### tank_fresh, tank_gray, tank_black, tank_lpg - Tank Levels

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Text |
| **Page** | Status_2 |
| **Font** | Font 2 (20pt) |
| **pco** | Dynamisch basierend auf Level |

**ESPHome:**
```cpp
float percent = (liters / tank_max) * 100.0;
char buffer[20];
snprintf(buffer, sizeof(buffer), "%.0fL (%.0f%%)", liters, percent);
id(nextion_display).set_component_text("tank_fresh", buffer);

// Farbe basierend auf Level
int color = (percent < 20) ? 63488 : (percent < 40) ? 64512 : 2016;
id(nextion_display).set_component_value("tank_fresh.pco", color);
```

---

## Optional: Advanced Components

### Progress Bar (Gauge)

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Progress Bar (j0) |
| **Name** | j0 |
| **Range** | 0-100 |
| **pco** | Farbe der Füllfarbe |

**ESPHome:**
```cpp
int percent = map(value, min, max, 0, 100);
id(nextion_display).set_component_value("j0.val", percent);
```

### Dual-State Button (On/Off)

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Dual-state Button |
| **Name** | btn_pump |
| **bco** | Dynamisch (Grün=ON, Rot=OFF) |
| **txt** | Dynamisch ("ON"/"OFF") |

**ESPHome:**
```cpp
if (state) {
  id(nextion_display).set_component_value("btn_pump.bco", 2016);  // Grün
  id(nextion_display).set_component_text("btn_pump", "ON");
} else {
  id(nextion_display).set_component_value("btn_pump.bco", 63488);  // Rot
  id(nextion_display).set_component_text("btn_pump", "OFF");
}
```

### Number (Slider)

| Eigenschaft | Wert |
|-------------|------|
| **Typ** | Number (n0) |
| **Range** | Min-Max definierbar |
| **pco** | Text-Farbe |

**ESPHome:**
```cpp
id(nextion_display).set_component_value("n0.val", value);
```

---

## Globale Variablen (Program.s)

### currentSection

| Typ | Wert | Beschreibung |
|-----|------|--------------|
| int | 0-5 | Aktuelle Sektion (0=Home, 1=Electric, ...) |

### currentPage

| Typ | Wert | Beschreibung |
|-----|------|--------------|
| int | 0-1 | Seiten-Index innerhalb Sektion |

### electricPages, waterPages, etc.

| Typ | Wert | Beschreibung |
|-----|------|--------------|
| int | 2 | Anzahl Seiten pro Sektion |

---

## Naming Conventions

### Komponenten-Namen

```
Prefix    Typ             Beispiel
──────    ─────────────   ───────────────
txt_      Text            txt_alert
btn_      Button          btn_electric
sich_     Sicherung       sich_1
tank_     Tank            tank_fresh
bat_      Batterie        bat_volt
j         Progress Bar    j0
n         Number          n0
```

### Seiten-Namen

```
Format: <Section>_<Number>

Beispiele:
Home          (Page 0)
Electric_1    (Page 1)
Electric_2    (Page 2)
Water_1       (Page 3)
```

---

## Farbcodes Referenz

| Farbe | Hex | Decimal | RGB | Verwendung |
|-------|-----|---------|-----|------------|
| Rot | #F800 | 63488 | 248,0,0 | Warning, Kritisch |
| Orange | #FC00 | 64512 | 252,0,0 | Caution, Warnung |
| Grün | #07E0 | 2016 | 0,124,0 | OK, Normal |
| Weiß | #FFFF | 65535 | 255,255,255 | Text Standard |
| Grau | #C618 | 50712 | 198,24,192 | Inaktiv |
| Transparent | #FFFF | 65535 | - | Background |

**RGB → Nextion Decimal Formel:**
```
Decimal = (R << 11) | (G << 5) | B

wobei:
R = 0-31 (5 bit)
G = 0-63 (6 bit)
B = 0-31 (5 bit)
```

---

## Best Practices

1. **Konsistente Benennung**: Nutze Prefixes (txt_, btn_, etc.)
2. **Transparenz**: Alle bco=65535 für transparenten Hintergrund
3. **Farb-Logik**: Grün=OK, Orange=Warnung, Rot=Kritisch
4. **Font-Größen**: Hierarchie beachten (12pt→32pt)
5. **Touch-Größe**: Mindestens 60x60px für Buttons

---

## Tools & Resources

- **Nextion Editor**: [Download](https://nextion.tech/nextion-editor/)
- **Color Calculator**: [Online Tool](https://nextion.tech/color-converter/)
- **Instruction Set**: [Docs](https://nextion.tech/instruction-set/)
- **Font Generator**: In Nextion Editor (File → Font Generator)

---

## Weitere Dokumentation

- [Page Structure](page-structure.md) - Komplette Seitenstruktur
- [Design Guide](design-guide.md) - Design-Prinzipien
- [Installation Guide](../../docs/installation.md) - Setup-Anleitung

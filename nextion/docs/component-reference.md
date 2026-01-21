# Nextion Component Reference

**Overview of All Nextion Components Used in SmartEBL Display**

---

## Standard Components (on Every Page)

### txt_alert - Scrolling Text (Master Warning/Caution)

| Property | Value | Description |
|----------|-------|-------------|
| **Type** | Scrolling Text | Scrolling text with automatic scroll |
| **Name** | txt_alert | Unique name |
| **Position** | x=0, y=0 | Top left |
| **Size** | w=800, h=50 | Full width |
| **Font** | Font 3 (24pt) | Highly readable |
| **bco** | 65535 | Transparent |
| **pco** | 2016 (Green) | Text color (dynamic) |
| **pco2** | 65535 | Transparent |
| **dir** | 0 | Left-to-right |
| **dis** | 1 | Scrolling enabled |
| **tim** | 50 | Scroll speed |
| **xcen** | 1 | Horizontally centered |
| **ycen** | 1 | Vertically centered |

**ESPHome Update:**
```cpp
id(nextion_display).set_component_text("txt_alert", "⚠️ MASTER WARNING");
id(nextion_display).set_component_value("txt_alert.pco", 63488);  // Red
```

---

### txt_page_counter - Page Counter

| Property | Value | Description |
|----------|-------|-------------|
| **Type** | Text | Static text |
| **Name** | txt_page_counter | Unique name |
| **Position** | x=700, y=10 | Top right |
| **Size** | w=80, h=30 | Compact |
| **Font** | Font 1 (16pt) | Standard |
| **bco** | 65535 | Transparent |
| **pco** | 65535 | White |
| **txt** | "1/2" | Example text |
| **xcen** | 2 | Right aligned |
| **ycen** | 1 | Vertically centered |

**Nextion Update (Page Preinit):**
```c
txt_page_counter.txt="1/2"
```

---

### Menu Buttons (5x)

#### btn_electric, btn_water, btn_climate, btn_status, btn_power

| Property | Value | Description |
|----------|-------|-------------|
| **Type** | Button | Touch button |
| **Position** | x=0, y=60/150/240/330/420 | Vertically staggered |
| **Size** | w=150, h=80 | Touch-friendly |
| **Font** | Font 2 (20pt) | Highly readable |
| **bco** | 65535 | Transparent |
| **pco** | 65535 | White (default) |
| **pco2** | 65535 | Transparent |
| **txt** | "Electric", "Water", etc. | Button text |

**Touch Release Event (Example btn_electric on Home):**
```c
currentSection=1
currentPage=0
page Electric_1
```

**Page Preinit (Highlighting Active Button):**
```c
btn_electric.pco=2016  // Green = Active
btn_water.pco=65535    // White = Inactive
```

---

## Electric Section Components

### bat_volt - Battery Voltage

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Name** | bat_volt |
| **Page** | Electric_1 |
| **Font** | Font 2 (20pt) |
| **bco** | 65535 (transparent) |
| **pco** | 65535 (white) |

**ESPHome:**
```cpp
char buffer[10];
snprintf(buffer, sizeof(buffer), "%.1fV", voltage);
id(nextion_display).set_component_text("bat_volt", buffer);
```

### bat_current - Battery Current

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Name** | bat_current |
| **Page** | Electric_1 |
| **Font** | Font 2 (20pt) |

**ESPHome:**
```cpp
snprintf(buffer, sizeof(buffer), "%.1fA", current);
id(nextion_display).set_component_text("bat_current", buffer);
```

### bat_soc - Battery State of Charge

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Name** | bat_soc |
| **Page** | Electric_1 |
| **Font** | Font 4 (32pt) |
| **pco** | Dynamic (Green/Orange/Red) |

**ESPHome:**
```cpp
snprintf(buffer, sizeof(buffer), "%.0f%%", soc);
id(nextion_display).set_component_text("bat_soc", buffer);

// Color based on SOC
int color = (soc < 20) ? 63488 : (soc < 40) ? 64512 : 2016;
id(nextion_display).set_component_value("bat_soc.pco", color);
```

### solar_power - Solar Power

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Name** | solar_power |
| **Page** | Electric_1 |

**ESPHome:**
```cpp
snprintf(buffer, sizeof(buffer), "%.0fW", power);
id(nextion_display).set_component_text("solar_power", buffer);
```

### shore_status - Shore Power Status

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Name** | shore_status |
| **Page** | Electric_1 |
| **pco** | Dynamic (Green=Connected, Gray=Disconnected) |

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

## Status Section Components

### sich_1 to sich_18 - Fuses

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Name** | sich_1, sich_2, ..., sich_18 |
| **Page** | Status_1 |
| **Layout** | Grid 3x6 |
| **Font** | Font 1 (16pt) |
| **pco** | Dynamic (Green=OK, Red=Defective) |

**Position (Example):**
```
sich_1:  x=170, y=70,  w=180, h=40
sich_2:  x=370, y=70,  w=180, h=40
sich_3:  x=570, y=70,  w=180, h=40
sich_4:  x=170, y=120, w=180, h=40
...
```

**ESPHome:**
```cpp
// For each fuse
int color = defect ? 63488 : 2016;  // Red : Green
id(nextion_display).set_component_value("sich_1.pco", color);
```

### tank_fresh, tank_gray, tank_black, tank_lpg - Tank Levels

| Property | Value |
|----------|-------|
| **Type** | Text |
| **Page** | Status_2 |
| **Font** | Font 2 (20pt) |
| **pco** | Dynamic based on level |

**ESPHome:**
```cpp
float percent = (liters / tank_max) * 100.0;
char buffer[20];
snprintf(buffer, sizeof(buffer), "%.0fL (%.0f%%)", liters, percent);
id(nextion_display).set_component_text("tank_fresh", buffer);

// Color based on level
int color = (percent < 20) ? 63488 : (percent < 40) ? 64512 : 2016;
id(nextion_display).set_component_value("tank_fresh.pco", color);
```

---

## Optional: Advanced Components

### Progress Bar (Gauge)

| Property | Value |
|----------|-------|
| **Type** | Progress Bar (j0) |
| **Name** | j0 |
| **Range** | 0-100 |
| **pco** | Fill color |

**ESPHome:**
```cpp
int percent = map(value, min, max, 0, 100);
id(nextion_display).set_component_value("j0.val", percent);
```

### Dual-State Button (On/Off)

| Property | Value |
|----------|-------|
| **Type** | Dual-state Button |
| **Name** | btn_pump |
| **bco** | Dynamic (Green=ON, Red=OFF) |
| **txt** | Dynamic ("ON"/"OFF") |

**ESPHome:**
```cpp
if (state) {
  id(nextion_display).set_component_value("btn_pump.bco", 2016);  // Green
  id(nextion_display).set_component_text("btn_pump", "ON");
} else {
  id(nextion_display).set_component_value("btn_pump.bco", 63488);  // Red
  id(nextion_display).set_component_text("btn_pump", "OFF");
}
```

### Number (Slider)

| Property | Value |
|----------|-------|
| **Type** | Number (n0) |
| **Range** | Min-Max definable |
| **pco** | Text color |

**ESPHome:**
```cpp
id(nextion_display).set_component_value("n0.val", value);
```

---

## Global Variables (Program.s)

### currentSection

| Type | Value | Description |
|------|-------|-------------|
| int | 0-5 | Current section (0=Home, 1=Electric, ...) |

### currentPage

| Type | Value | Description |
|------|-------|-------------|
| int | 0-1 | Page index within section |

### electricPages, waterPages, etc.

| Type | Value | Description |
|------|-------|-------------|
| int | 2 | Number of pages per section |

---

## Naming Conventions

### Component Names

```
Prefix    Type             Example
──────    ─────────────   ───────────────
txt_      Text            txt_alert
btn_      Button          btn_electric
sich_     Fuse            sich_1
tank_     Tank            tank_fresh
bat_      Battery         bat_volt
j         Progress Bar    j0
n         Number          n0
```

### Page Names

```
Format: <Section>_<Number>

Examples:
Home          (Page 0)
Electric_1    (Page 1)
Electric_2    (Page 2)
Water_1       (Page 3)
```

---

## Color Code Reference

| Color | Hex | Decimal | RGB | Usage |
|-------|-----|---------|-----|-------|
| Red | #F800 | 63488 | 248,0,0 | Warning, Critical |
| Orange | #FC00 | 64512 | 252,0,0 | Caution, Warning |
| Green | #07E0 | 2016 | 0,124,0 | OK, Normal |
| White | #FFFF | 65535 | 255,255,255 | Text Standard |
| Gray | #C618 | 50712 | 198,24,192 | Inactive |
| Transparent | #FFFF | 65535 | - | Background |

**RGB → Nextion Decimal Formula:**
```
Decimal = (R << 11) | (G << 5) | B

where:
R = 0-31 (5 bit)
G = 0-63 (6 bit)
B = 0-31 (5 bit)
```

---

## Best Practices

1. **Consistent Naming**: Use prefixes (txt_, btn_, etc.)
2. **Transparency**: All bco=65535 for transparent background
3. **Color Logic**: Green=OK, Orange=Warning, Red=Critical
4. **Font Sizes**: Observe hierarchy (12pt→32pt)
5. **Touch Size**: Minimum 60x60px for buttons

---

## Tools & Resources

- **Nextion Editor**: [Download](https://nextion.tech/nextion-editor/)
- **Color Calculator**: [Online Tool](https://nextion.tech/color-converter/)
- **Instruction Set**: [Docs](https://nextion.tech/instruction-set/)
- **Font Generator**: In Nextion Editor (File → Font Generator)

---

## Further Documentation

- [Page Structure](page-structure.md) - Complete page structure
- [Design Guide](design-guide.md) - Design principles
- [Installation Guide](../../docs/installation.md) - Setup instructions

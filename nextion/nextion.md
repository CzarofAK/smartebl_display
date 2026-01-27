# Nextion Display Guide

Complete guide for creating and configuring the Nextion display interface for SmartEBL Display.

---

## Table of Contents

1. [Display Specifications](#display-specifications)
2. [Getting Started](#getting-started)
3. [Page Structure Overview](#page-structure-overview)
4. [Global Variables](#global-variables)
5. [Complete Page Guide](#complete-page-guide)
6. [Component Reference](#component-reference)
7. [Navigation System](#navigation-system)
8. [Best Practices](#best-practices)
9. [Troubleshooting](#troubleshooting)

---

## Display Specifications

### Recommended Model

- **Model**: NX8048P070 (7" Enhanced)
- **Resolution**: 800 x 480 pixels
- **Orientation**: Horizontal
- **UART Baud Rate**: 115200
- **Touch**: Resistive touch screen
- **Memory**: 16MB Flash

### Alternative Models

- **NX8048T070** (Basic) - Works but has fewer features
- **NX8048K070** (Discovery) - Not recommended (limited memory)

---

## Getting Started

### Step 1: Install Nextion Editor

1. Download from [https://nextion.tech/nextion-editor/](https://nextion.tech/nextion-editor/)
2. Install for your OS (Windows/Mac)
3. Launch Nextion Editor

### Step 2: Create New Project

1. **File → New**
2. Select **Nextion Enhanced**
3. Choose **NX8048P070**
4. Resolution: **800 x 480**
5. Orientation: **Horizontal**
6. Click **OK**

### Step 3: Configure Project Settings

1. Go to **Program.s** (in Page tree)
2. Add global variables (see [Global Variables](#global-variables))

---

## Page Structure Overview

The SmartEBL Display uses **11 pages** organized into 6 sections:

| Page ID | Name | Description |
|---------|------|-------------|
| 0 | Home | Main menu with 5 section buttons |
| 1-2 | Electric_1, Electric_2 | Electrical systems (battery, solar, shore) |
| 3-4 | Water_1, Water_2 | Water systems (tanks, pumps) |
| 5-6 | Climate_1, Climate_2 | Climate & heating |
| 7-8 | Status_1, Status_2 | System status & alarms |
| 9-10 | Power_1, Power_2 | Energy & power management |

### Navigation Flow

```
                     HOME (Page 0)
                          │
      ┌───────────┬────────┼────────┬───────────┐
      │           │        │        │           │
   ELECTRIC    WATER   CLIMATE   STATUS      POWER
  (Pages 1-2) (3-4)    (5-6)    (7-8)      (9-10)
```

---

## Global Variables

Add these to **Program.s**:

```c
// Current section tracker
// 0=Home, 1=Electric, 2=Water, 3=Climate, 4=Status, 5=Power
int currentSection=0

// Current page within section (0=first page, 1=second page)
int currentPage=0

// Number of pages per section
int electricPages=2
int waterPages=2
int climatePages=2
int statusPages=2
int powerPages=2
```

### Color Definitions

| Color | Hex | Decimal | Usage |
|-------|-----|---------|-------|
| Red | `#F800` | 63488 | Master WARNING / Critical |
| Orange | `#FC00` | 64512 | Master CAUTION / Warning |
| Green | `#07E0` | 2016 | Normal / Active / OK |
| White | `#FFFF` | 65535 | Text / Inactive |
| Gray | `#C618` | 50712 | Disabled / Inactive |

---

## Complete Page Guide

### Page 0: Home (Main Menu)

#### Components

**1. Scrolling Text - Master Warning/Caution**

| Property | Value |
|----------|-------|
| Type | Scrolling Text |
| Name | `txt_alert` |
| Position | x=0, y=0 |
| Size | w=800, h=50 |
| Font | Font 3 (24pt) |
| bco | 65535 (transparent) |
| pco | 2016 (green - changes dynamically) |
| pco2 | 65535 (transparent) |
| dir | 0 (left-to-right) |
| dis | 1 (scrolling enabled) |
| tim | 50 (scroll speed) |
| xcen | 1 (horizontally centered) |
| ycen | 1 (vertically centered) |

**Initial Text**: `✓ ALL SYSTEMS NORMAL`

---

**2. Menu Buttons (5 vertical buttons)**

Each button follows this template:

| Property | Value |
|----------|-------|
| Type | Button |
| Size | w=150, h=80 |
| Font | Font 2 (20pt) |
| bco | 65535 (transparent) |
| pco | 65535 (white - default) |
| pco2 | 65535 (transparent) |

**Button Positions:**

| Button | Name | Position | Text | Touch Event |
|--------|------|----------|------|-------------|
| Electric | `btn_electric` | x=0, y=60 | "Electric" | See below |
| Water | `btn_water` | x=0, y=150 | "Water" | See below |
| Climate | `btn_climate` | x=0, y=240 | "Climate" | See below |
| Status | `btn_status` | x=0, y=330 | "Status" | See below |
| Power | `btn_power` | x=0, y=420 | "Power" | See below |

**Touch Release Events:**

```c
// btn_electric
currentSection=1
currentPage=0
page Electric_1

// btn_water
currentSection=2
currentPage=0
page Water_1

// btn_climate
currentSection=3
currentPage=0
page Climate_1

// btn_status
currentSection=4
currentPage=0
page Status_1

// btn_power
currentSection=5
currentPage=0
page Power_1
```

---

**Page Preinit Event (Home):**

```c
// Reset all buttons to white
btn_electric.pco=65535
btn_water.pco=65535
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Reset tracking
currentSection=0
currentPage=0
```

---

### Pages 1-10: Section Pages (Electric, Water, Climate, Status, Power)

All section pages share the same structure. I'll use Electric as the example.

#### Common Components (on ALL section pages)

**1. Scrolling Text** (Copy from Home)
- Name: `txt_alert`
- Same properties as Home page

**2. Page Counter**

| Property | Value |
|----------|-------|
| Type | Text |
| Name | `txt_page_counter` |
| Position | x=700, y=10 |
| Size | w=80, h=30 |
| Font | Font 1 (16pt) |
| bco | 65535 (transparent) |
| pco | 65535 (white) |
| txt | "1/2" or "2/2" |
| xcen | 2 (right aligned) |
| ycen | 1 (centered) |

**3. Menu Buttons** (Copy from Home)
- All 5 buttons: `btn_electric`, `btn_water`, `btn_climate`, `btn_status`, `btn_power`
- Same positions and properties

**Menu Button Touch Events (Generic Pattern):**

```c
// btn_electric - Navigate within Electric section
currentPage++
if(currentPage>=electricPages)
{
  currentPage=0
}
page Electric_2  // Or back to Electric_1

// btn_water - Jump to Water section
currentSection=2
currentPage=0
page Water_1

// btn_climate - Jump to Climate section
currentSection=3
currentPage=0
page Climate_1

// btn_status - Jump to Status section
currentSection=4
currentPage=0
page Status_1

// btn_power - Jump to Power section
currentSection=5
currentPage=0
page Power_1
```

**4. Next Page Button** (Optional, on first page of each section)

| Property | Value |
|----------|-------|
| Type | Button |
| Name | `btn_next` |
| Position | x=650, y=400 |
| Size | w=120, h=50 |
| Text | "Next →" |
| Font | Font 2 (20pt) |
| bco | 65535 (transparent) |
| pco | 65535 (white) |

**Touch Release Event:**
```c
currentPage=1
page Electric_2  // Go to second page
```

---

#### Page-Specific Components

**Electric_1 Components:**

| Name | Type | Purpose | Updated by ESPHome |
|------|------|---------|-------------------|
| `bat_volt` | Text | Battery Voltage | ✅ |
| `bat_current` | Text | Battery Current | ✅ |
| `bat_soc` | Text | State of Charge | ✅ (with color) |
| `solar_power` | Text | Solar Power | ✅ |
| `shore_status` | Text | Shore Connected/Disconnected | ✅ |
| `alt_power` | Text | Alternator Power | ✅ |
| `alt_status` | Text | Alternator Status | ✅ |

**Status_1 Components (Fuses):**

Grid layout for 18 fuses:

| Component | Position | Purpose |
|-----------|----------|---------|
| `sich_1` to `sich_18` | 3 columns x 6 rows | Fuse status indicators |

**Example Positions:**
```
sich_1:  x=170, y=70,  w=180, h=40
sich_2:  x=370, y=70,  w=180, h=40
sich_3:  x=570, y=70,  w=180, h=40
sich_4:  x=170, y=120, w=180, h=40
...
```

**Status_2 Components (Tanks):**

| Name | Type | Purpose |
|------|------|---------|
| `tank_fresh` | Text | Fresh water level |
| `tank_gray` | Text | Gray water level |
| `tank_black` | Text | Black water level |
| `tank_lpg` | Text | LPG gas level |

---

#### Page Preinit Events (Generic Pattern)

**For First Page (e.g., Electric_1):**

```c
// Highlight active button (Green)
btn_electric.pco=2016  // Green = Active
btn_water.pco=65535    // White = Inactive
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Update page counter
txt_page_counter.txt="1/2"

// Update tracking
currentSection=1
currentPage=0
```

**For Second Page (e.g., Electric_2):**

```c
// Same button highlighting
btn_electric.pco=2016
btn_water.pco=65535
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Update page counter
txt_page_counter.txt="2/2"

// Update tracking
currentSection=1
currentPage=1
```

---

## Component Reference

### Standard Text Component

Use for displaying values from ESPHome.

**Recommended Settings:**

| Property | Recommended Value |
|----------|-------------------|
| bco | 65535 (transparent background) |
| pco | 65535 (white text, or dynamic) |
| Font | Font 1 (16pt) or Font 2 (20pt) |
| xcen | 0 (left), 1 (center), or 2 (right) |
| ycen | 1 (vertically centered) |

**Example:**
- Name: `bat_volt`
- Position: x=200, y=100
- Size: w=150, h=40
- Font: Font 2
- Text: "12.8V" (placeholder)

**ESPHome Update:**
```cpp
char buffer[10];
snprintf(buffer, sizeof(buffer), "%.1fV", voltage);
id(nextion_display).set_component_text("bat_volt", buffer);
```

---

### Scrolling Text Component

Use for Master Warning/Caution text.

**Settings:**

| Property | Value |
|----------|-------|
| dis | 1 (scrolling enabled) |
| tim | 50 (scroll speed - lower = faster) |
| dir | 0 (left-to-right) |
| dis | 1 (enable scrolling) |

**When to scroll:** Text scrolls automatically when text width > component width

---

### Button Component

Use for navigation and controls.

**Touch Area Recommendations:**
- Minimum size: 60x60 px
- Recommended: 80x80 px or larger
- Menu buttons: 150x80 px

**Properties:**
- `bco`: Background color (65535 for transparent)
- `pco`: Text color
- `pco2`: Press color (usually same as pco)

---

### Progress Bar (Gauge)

Use for visual percentage display.

**Component Type:** Progress Bar (`j0`, `j1`, etc.)

**Properties:**
- Range: 0-100
- `pco`: Fill color
- `bco`: Background color

**ESPHome Update:**
```cpp
int percent = map(value, min_val, max_val, 0, 100);
id(nextion_display).set_component_value("j0.val", percent);
```

---

### Dual-State Button

Use for ON/OFF controls.

**ESPHome Pattern:**
```cpp
if (state) {
  id(nextion_display).set_component_value("btn_pump.bco", 2016);  // Green
  id(nextion_display).set_component_text("btn_pump", "ON");
} else {
  id(nextion_display).set_component_value("btn_pump.bco", 63488);  // Red
  id(nextion_display).set_component_text("btn_pump", "OFF");
}
```

---

## Navigation System

### Horizontal Navigation (Between Sections)

User clicks menu buttons on the left to jump between sections:
- Electric → Water → Climate → Status → Power

### Vertical Navigation (Within Section)

User clicks the active section button again, or "Next" button:
- Electric_1 → Electric_2 → Electric_1 (loops)

### Navigation Pattern Example

```c
// On Electric_1, user clicks btn_electric
currentPage++
if(currentPage>=electricPages)  // electricPages=2
{
  currentPage=0  // Loop back
}
page Electric_2  // Or Electric_1 if looping
```

---

## Best Practices

### 1. Reusable Components

**Workflow:**
1. Create Home page completely
2. Copy scrolling text + 5 menu buttons
3. Paste on each section page
4. Adjust touch events only

**Quick Copy:**
- Right-click component → Copy
- Go to target page → Right-click → Paste
- Adjust position/name

---

### 2. Consistent Naming

```
Pages:      Electric_1, Electric_2, Water_1, ...
Buttons:    btn_electric, btn_water, btn_next
Text:       txt_alert, txt_page_counter
Values:     bat_volt, tank_fresh, temp_inside
Fuses:      sich_1, sich_2, ...
```

---

### 3. Transparency for All Components

For **every** button/text:
```
bco=65535   (Background transparent)
pco=65535   (Text white by default)
pco2=65535  (Press color transparent)
```

This creates the clean, minimalist design.

---

### 4. Touch Event Structure

**Always include:**
```c
currentSection=X  // Tracking (0-5)
currentPage=Y     // Tracking (0-1)
page PageName     // Navigation
```

---

### 5. Font Hierarchy

| Font ID | Size | Usage | Example |
|---------|------|-------|---------|
| Font 0 | 12pt | Small labels, details | "Voltage:" |
| Font 1 | 16pt | Standard text, values | "12.8V" |
| Font 2 | 20pt | Buttons, important text | "Electric" |
| Font 3 | 24pt | Scrolling text, headings | "MASTER WARNING" |
| Font 4 | 32pt | Large values, numbers | "78%" |

**Creating Fonts:**
1. File → Font Generator
2. Choose Font: Roboto or Arial
3. Create sizes: 12, 16, 20, 24, 32pt
4. Enable Anti-Aliasing
5. Format: 8-bit for color support

---

### 6. HMI File Backup

⚠️ **CRITICAL**: Nextion .HMI files are binary and **cannot be version controlled**!

**Create regular backups:**
```
Project_v1.0.HMI
Project_v1.1.HMI
Project_v1.2.HMI
Project_WORKING.HMI (always keep working version)
```

---

## Troubleshooting

### "Component Not Found" Error

**Cause:** Component name misspelled or doesn't exist on current page

**Solution:**
- Check exact spelling (case-sensitive!)
- Verify component exists on visible page
- Use ESPHome logs to see what's being sent:
  ```cpp
  ESP_LOGI("nextion", "Setting bat_volt to %.1fV", voltage);
  ```

---

### Scrolling Doesn't Work

**Cause:** `dis=0` or text shorter than component width

**Solution:**
- Set `dis=1` in component properties
- Ensure text is longer than component width
- Adjust `tim` value (lower = faster scroll)

---

### Button Doesn't Respond

**Cause:** Touch event not configured or page ID wrong

**Solution:**
- Check Touch Release Event is set
- Verify target page name/ID exists
- Test with page ID instead: `page 1` instead of `page Electric_1`

---

### Transparency Shows Black Background

**Cause:** `bco` not set to 65535

**Solution:**
- Set all `bco` (background color) to 65535
- Check in **Attribute** panel for each component

---

### Touch Calibration Off

**Solution:**
1. Open Nextion Editor
2. Tools → Touch Calibrate
3. Follow on-screen instructions
4. Upload recalibrated firmware

---

## Compiling and Flashing

### Compile HMI to TFT

1. **File → Compile**
2. Select your model: **NX8048P070**
3. Output file: `project_name.tft`

### Flash to Display

**Method 1: MicroSD Card (Recommended)**

1. Format microSD as FAT32
2. Copy `.tft` file to SD root
3. **Rename to short name** (e.g., `display.tft`)
4. Insert SD into Nextion
5. Power on - auto-updates
6. Remove SD when done

**Method 2: USB-Serial**

1. Connect USB-Serial adapter to Nextion (RX/TX/GND)
2. In Nextion Editor: **File → Upload**
3. Select COM port, baud 115200
4. Click Upload

---

## Testing Checklist

- [ ] All pages created (0-10)
- [ ] All buttons lead to correct pages
- [ ] Page counters show correct values (1/2, 2/2)
- [ ] Transparency works (no backgrounds visible)
- [ ] Scrolling text scrolls for long messages
- [ ] Navigation between sections works
- [ ] Navigation within sections works
- [ ] Page Preinit events highlight correct buttons
- [ ] Touch areas respond correctly
- [ ] UART test: Send `page 0` via serial - display goes to Home

---

## Next Steps

1. ✅ Nextion HMI created and flashed
2. → Configure ESPHome - See [esphome.md](../esphome/esphome.md)
3. → Customize design - See [design.md](../docs/design.md)
4. → Install system - See [installation.md](../docs/installation.md)

---

## Resources

- **[Nextion Editor Download](https://nextion.tech/nextion-editor/)**
- **[Nextion Instruction Set](https://nextion.tech/instruction-set/)**
- **[Nextion Forum](https://forum.nextion.tech/)**
- **[Color Converter](https://nextion.tech/color-converter/)**
- **[ESPHome Nextion Docs](https://esphome.io/components/display/nextion.html)**

---

## Support

Need help?

- 📖 **[Installation Guide](../docs/installation.md)**
- 📖 **[Design Guide](../docs/design.md)**
- 🐛 **[GitHub Issues](https://github.com/CzarofAK/smartebl_display/issues)**
- 💬 **[Discussions](https://github.com/CzarofAK/smartebl_display/discussions)**

**Good luck creating your Nextion display! 🎨**

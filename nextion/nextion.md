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
| 3-4 | Tanks_1, Tanks_2 | All fluid levels (fresh/waste water, diesel, AdBlue, gas) + pump control |
| 5-6 | Climate_1, Climate_2 | Climate & heating |
| 7-8 | Status_1, Status_2 | System status & alarms |
| 9-10 | Power_1, Power_2 | Energy & power management |

### Navigation Flow

```
                     HOME (Page 0)
                          │
      ┌───────────┬────────┼────────┬───────────┐
      │           │        │        │           │
   ELECTRIC    TANKS   CLIMATE   STATUS      POWER
  (Pages 1-2) (3-4)    (5-6)    (7-8)      (9-10)
```

---

## Global Variables

Add these to **Program.s**:

```c
// Current section tracker
// 0=Home, 1=Electric, 2=Tanks, 3=Climate, 4=Status, 5=Power
int currentSection=0

// Current page within section (0=first page, 1=second page)
int currentPage=0

// Number of pages per section
int electricPages=2
int tanksPages=2
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

**1. Status Bar - Time/Date and Temperature Display**

| Property | Value |
|----------|-------|
| Type | Text / Scrolling Text (when in alert mode) |
| Name | `txt_status_bar` |
| Position | x=0, y=0 |
| Size | w=800, h=80 |
| Font | Font 2 (20pt) |
| bco | 65535 (transparent) |
| pco | 2016 (green - changes to orange/red in alert states) |
| pco2 | 65535 (transparent) |
| dir | 0 (left-to-right) |
| dis | 1 (scrolling enabled when in alert mode) |
| tim | 50 (scroll speed for alerts) |
| xcen | 0 (left aligned in normal mode, centered in alert mode) |
| ycen | 1 (vertically centered) |

**Normal State Text**: `12:34  Jan 28                         Inside: 21°C  Outside: 5°C`
**Caution State Text**: `⚡ MASTER CAUTION: [caution messages]`
**Warning State Text**: `⚠️ MASTER WARNING: [warning messages]`

**Implementation Notes:**
- Normal state: Left side shows time & date, right side shows inside/outside temperatures
- Alert state: Full-width scrolling text with warning/caution messages
- Text alignment changes based on state (left-aligned for normal, centered for alerts)

---

**2. Menu Buttons (5 vertical buttons)**

Each button follows this template:

| Property | Value |
|----------|-------|
| Type | Button |
| Size | w=100, h=80 |
| Font | Font 2 (20pt) |
| bco | 65535 (transparent) |
| pco | 65535 (white - default) |
| pco2 | 65535 (transparent) |

**Button Positions:**

| Button | Name | Position | Text | Touch Event |
|--------|------|----------|------|-------------|
| Electric | `btn_electric` | x=0, y=80 | "Electric" | See below |
| Tanks | `btn_tanks` | x=0, y=160 | "Tanks" | See below |
| Climate | `btn_climate` | x=0, y=240 | "Climate" | See below |
| Status | `btn_status` | x=0, y=320 | "Status" | See below |
| Power | `btn_power` | x=0, y=400 | "Power" | See below |

**3. Page Indicators (displayed on active button only)**

| Property | Value |
|----------|-------|
| Type | Text |
| Size | w=100, h=30 |
| Font | Font 1 (16pt) |
| bco | 65535 (transparent) |
| pco | 2016 (green - matches active button) |
| xcen | 1 (centered) |
| ycen | 1 (centered) |

**Page Indicator Positions (below section name on button):**

| Indicator | Name | Position | Text |
|-----------|------|----------|------|
| Electric | `txt_electric_page` | x=0, y=105 | "1/2" or "2/2" |
| Tanks | `txt_tanks_page` | x=0, y=185 | "1/2" or "2/2" |
| Climate | `txt_climate_page` | x=0, y=265 | "1/2" or "2/2" |
| Status | `txt_status_page` | x=0, y=345 | "1/2" or "2/2" |
| Power | `txt_power_page` | x=0, y=425 | "1/2" or "2/2" |

**Implementation Note:**
- Page indicators are only visible when that section is active
- Hide all page indicators when navigating to a different section
- Update page indicator text when cycling through pages within a section

**Touch Release Events:**

```c
// btn_electric
currentSection=1
currentPage=0
page Electric_1

// btn_tanks
currentSection=2
currentPage=0
page Tanks_1

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
btn_tanks.pco=65535
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Hide all page indicators
txt_electric_page.txt=""
txt_tanks_page.txt=""
txt_climate_page.txt=""
txt_status_page.txt=""
txt_power_page.txt=""

// Reset tracking
currentSection=0
currentPage=0
```

---

### Pages 1-10: Section Pages (Electric, Water, Climate, Status, Power)

All section pages share the same structure. I'll use Electric as the example.

#### Common Components (on ALL section pages)

**1. Status Bar** (Copy from Home)
- Name: `txt_status_bar`
- Same properties as Home page
- Height: 80px
- Displays time/date/temps in normal state, alerts in warning/caution states

**2. Menu Buttons** (Copy from Home)
- All 5 buttons: `btn_electric`, `btn_tanks`, `btn_climate`, `btn_status`, `btn_power`
- Width: 100px, Height: 80px each
- Same positions and properties

**3. Page Indicators** (Copy from Home)
- All 5 indicators: `txt_electric_page`, `txt_tanks_page`, `txt_climate_page`, `txt_status_page`, `txt_power_page`
- Only visible on active section's button
- Positioned below section name on button

**Menu Button Touch Events (Generic Pattern):**

```c
// btn_electric - Navigate within Electric section if already active, or jump to it
if(currentSection==1)
{
  currentPage++
  if(currentPage>=electricPages)
  {
    currentPage=0
  }
  if(currentPage==0)
  {
    page Electric_1
  }else{
    page Electric_2
  }
}else{
  currentSection=1
  currentPage=0
  page Electric_1
}

// btn_tanks - Jump to Tanks section or cycle through Tanks pages
if(currentSection==2)
{
  currentPage++
  if(currentPage>=tanksPages)
  {
    currentPage=0
  }
  if(currentPage==0)
  {
    page Tanks_1
  }else{
    page Tanks_2
  }
}else{
  currentSection=2
  currentPage=0
  page Tanks_1
}

// btn_climate - Jump to Climate section or cycle through Climate pages
if(currentSection==3)
{
  currentPage++
  if(currentPage>=climatePages)
  {
    currentPage=0
  }
  if(currentPage==0)
  {
    page Climate_1
  }else{
    page Climate_2
  }
}else{
  currentSection=3
  currentPage=0
  page Climate_1
}

// btn_status - Jump to Status section or cycle through Status pages
if(currentSection==4)
{
  currentPage++
  if(currentPage>=statusPages)
  {
    currentPage=0
  }
  if(currentPage==0)
  {
    page Status_1
  }else{
    page Status_2
  }
}else{
  currentSection=4
  currentPage=0
  page Status_1
}

// btn_power - Jump to Power section or cycle through Power pages
if(currentSection==5)
{
  currentPage++
  if(currentPage>=powerPages)
  {
    currentPage=0
  }
  if(currentPage==0)
  {
    page Power_1
  }else{
    page Power_2
  }
}else{
  currentSection=5
  currentPage=0
  page Power_1
}
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

**Tanks Section (Pages 3-4) - Detailed Layout:**

The Tanks section provides comprehensive monitoring of all fluid levels in the motorhome.

**Page 3: Tanks_1 - Complete Fluid Overview**

This page displays all tank and fluid levels on a single overview page with quick pump control access.

| Component | Type | Position | Purpose | ESPHome Update |
|-----------|------|----------|---------|----------------|
| `tank_fresh` | Text/Gauge | x=120, y=100 | Fresh water tank level | ✅ |
| `tank_waste` | Text/Gauge | x=420, y=100 | Waste water tank level | ✅ |
| `tank_diesel` | Text/Gauge | x=120, y=200 | Diesel tank level | ✅ |
| `tank_adblue` | Text/Gauge | x=420, y=200 | AdBlue tank level | ✅ |
| `tank_gas1` | Text/Gauge | x=120, y=300 | Gas bottle 1 level | ✅ |
| `tank_gas2` | Text/Gauge | x=420, y=300 | Gas bottle 2 level | ✅ |
| `btn_pump` | Button | x=300, y=380 | Water pump ON/OFF switch | ✅ (bidirectional) |

**Design Philosophy:**
- All critical fluid levels visible at a glance
- Water pump switch prominently placed for quick access
- Grid layout (2 columns × 3 rows) for organized presentation
- Color-coded levels (Green=OK, Orange=Low, Red=Critical)

**Page 4: Tanks_2 - External Fill Display**

This page is optimized for viewing through the outside window while filling fresh water from external connections.

| Component | Type | Position | Purpose | ESPHome Update |
|-----------|------|----------|---------|----------------|
| `tank_fresh_large` | Text/Gauge | x=200, y=100 | Extra-large fresh water level display | ✅ |
| `txt_fresh_percent` | Text | x=350, y=250 | Large percentage display (e.g., "78%") | ✅ |

**Design Philosophy:**
- Maximized font size for visibility from outside
- High contrast colors
- Minimal UI elements (focus on fresh water only)
- Gauge + percentage for clear indication

---

#### Page Preinit Events (Generic Pattern)

**For First Page (e.g., Electric_1):**

```c
// Highlight active button (Green text, no underline)
btn_electric.pco=2016  // Green = Active
btn_tanks.pco=65535    // White = Inactive
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Show page indicator only for active section
txt_electric_page.txt="1/2"
txt_electric_page.pco=2016
// Hide other page indicators
txt_tanks_page.txt=""
txt_climate_page.txt=""
txt_status_page.txt=""
txt_power_page.txt=""

// Update tracking
currentSection=1
currentPage=0
```

**For Second Page (e.g., Electric_2):**

```c
// Same button highlighting
btn_electric.pco=2016
btn_tanks.pco=65535
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Update page indicator for active section
txt_electric_page.txt="2/2"
txt_electric_page.pco=2016
// Hide other page indicators
txt_tanks_page.txt=""
txt_climate_page.txt=""
txt_status_page.txt=""
txt_power_page.txt=""

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
- [ ] Page indicators show correct values (1/2, 2/2) on active button only
- [ ] Page indicators hidden when switching to different section
- [ ] Transparency works (no backgrounds visible)
- [ ] Status bar displays time/date/temps in normal mode
- [ ] Status bar switches to scrolling alerts in warning/caution mode
- [ ] Navigation between sections works (via button press)
- [ ] Navigation within sections works (via same button press when already active)
- [ ] Page Preinit events highlight correct buttons (green text, no underline)
- [ ] Touch areas respond correctly (100px wide buttons)
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

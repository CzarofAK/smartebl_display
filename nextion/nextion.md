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
| 3-4 | Levels_1, Levels_2 | All fluid levels (fresh/waste water, diesel, AdBlue, gas) + pump control |
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
// 0=Home, 1=Electric, 2=Levels, 3=Climate, 4=Status, 5=Power
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

**Normal State Text**: `12:34:56 28.Jan.2025                         Inside: 21°C  Outside: 5°C`
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
| Levels | `btn_tanks` | x=0, y=160 | "Levels" | See below |
| Climate | `btn_climate` | x=0, y=240 | "Climate" | See below |
| Status | `btn_status` | x=0, y=320 | "Status" | See below |
| Power | `btn_power` | x=0, y=400 | "Power" | See below |

**3. Section Text Labels (on buttons - FIXED POSITION)**

| Property | Value |
|----------|-------|
| Type | Text |
| Size | w=100, h=30 |
| Font | Font 2 (20pt) |
| bco | 65535 (transparent) |
| pco | 65535 (white - 2016 green when active) |
| xcen | 1 (centered) |
| ycen | 0 (top aligned) |

**Section Label Positions (FIXED - top of button):**

| Label | Name | Position | Text |
|-------|------|----------|------|
| Electric | `txt_electric` | x=0, y=90 (10px from button top) | "Electric" |
| Levels | `txt_levels` | x=0, y=170 (10px from button top) | "Levels" |
| Climate | `txt_climate` | x=0, y=250 (10px from button top) | "Climate" |
| Status | `txt_status` | x=0, y=330 (10px from button top) | "Status" |
| Power | `txt_power` | x=0, y=410 (10px from button top) | "Power" |

**4. Page Indicators (FIXED POSITION - always present)**

| Property | Value |
|----------|-------|
| Type | Text |
| Size | w=100, h=20 |
| Font | Font 1 (16pt) |
| bco | 65535 (transparent) |
| pco | 2016 (green - matches active button) |
| xcen | 1 (centered) |
| ycen | 0 (top aligned) |

**Page Indicator Positions (FIXED - bottom of button):**

| Indicator | Name | Position | Text |
|-----------|------|----------|------|
| Electric | `txt_electric_page` | x=0, y=135 (55px from button top) | "1/2" or "2/2" or "" |
| Levels | `txt_levels_page` | x=0, y=215 (55px from button top) | "1/2" or "2/2" or "" |
| Climate | `txt_climate_page` | x=0, y=295 (55px from button top) | "1/2" or "2/2" or "" |
| Status | `txt_status_page` | x=0, y=375 (55px from button top) | "1/2" or "2/2" or "" |
| Power | `txt_power_page` | x=0, y=455 (55px from button top) | "1/2" or "2/2" or "" |

**Implementation Notes:**
- **CRITICAL**: Section text labels and page indicators use SEPARATE text components with FIXED positions
- Section text always stays at y+10 from button top (never moves)
- Page indicator always stays at y+55 from button top (never moves)
- When section is inactive: page indicator txt="" (empty string = invisible)
- When section is active: page indicator shows "1/2", "2/2", etc.
- This prevents text jumping when switching between pages
- Both text components are always present (just hidden via txt="")
- Color changes: txt_electric.pco=2016 when active, =65535 when inactive

**Touch Release Events:**

```c
// btn_electric
currentSection=1
currentPage=0
page Electric_1

// btn_tanks
currentSection=2
currentPage=0
page Levels_1

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

**Menu Button Touch Events (Updated - Returns to Home from Last Page):**

```c
// btn_electric - Cycles through pages, returns to home when pressing on last page
if(currentSection==1)
{
  currentPage++
  if(currentPage>=electricPages)  // Beyond last page?
  {
    // Return to home instead of looping
    currentSection=0
    currentPage=0
    page Home
  }else{
    // Navigate to next page within section
    if(currentPage==0)
    {
      page Electric_1
    }else if(currentPage==1)
    {
      page Electric_2
    }
  }
}else{
  // Jump to first page of section
  currentSection=1
  currentPage=0
  page Electric_1
}

// btn_levels - Jump to Levels section or cycle through Levels pages
if(currentSection==2)
{
  currentPage++
  if(currentPage>=levelsPages)  // Beyond last page?
  {
    // Return to home
    currentSection=0
    currentPage=0
    page Home
  }else{
    // Navigate to next page
    if(currentPage==0)
    {
      page Levels_1
    }else if(currentPage==1)
    {
      page Levels_2
    }
  }
}else{
  currentSection=2
  currentPage=0
  page Levels_1
}

// btn_climate - Jump to Climate section or cycle through Climate pages
if(currentSection==3)
{
  currentPage++
  if(currentPage>=climatePages)  // Beyond last page?
  {
    // Return to home
    currentSection=0
    currentPage=0
    page Home
  }else{
    if(currentPage==0)
    {
      page Climate_1
    }else if(currentPage==1)
    {
      page Climate_2
    }
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
  if(currentPage>=statusPages)  // Beyond last page?
  {
    // Return to home
    currentSection=0
    currentPage=0
    page Home
  }else{
    if(currentPage==0)
    {
      page Status_1
    }else if(currentPage==1)
    {
      page Status_2
    }
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
  if(currentPage>=powerPages)  // Beyond last page?
  {
    // Return to home
    currentSection=0
    currentPage=0
    page Home
  }else{
    if(currentPage==0)
    {
      page Power_1
    }else if(currentPage==1)
    {
      page Power_2
    }
  }
}else{
  currentSection=5
  currentPage=0
  page Power_1
}
```

**Key Changes:**
- When pressing a section button while on the last page of that section, the display now returns to Home instead of looping back to page 1
- This provides a more intuitive navigation: Section → Page 1 → Page 2 → Home
- Still allows direct jumping between sections (e.g., from Electric to Levels without going through Home)

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

**Electric_1 Components (Victron-Style Power Flow Overview):**

This page displays a complete power flow diagram inspired by Victron Venus OS.

**Power Source Boxes (Top Row):**

| Component | Type | Position | Size | Purpose | ESPHome Update |
|-----------|------|----------|------|---------|----------------|
| `box_shore` | Rectangle | x=120, y=100 | w=150, h=100 | Shore power box background | Static |
| `txt_shore_label` | Text | x=130, y=110 | - | "⚡ Shore" label | Static |
| `txt_shore_power` | Text | x=130, y=150 | - | Shore power (e.g., "91W") | ✅ |
| `box_inverter` | Rectangle | x=330, y=100 | w=220, h=120 | Inverter/Charger box | Static |
| `txt_inverter_label` | Text | x=350, y=110 | - | "🔌 Inverter / Charger" | Static |
| `txt_inverter_status` | Text | x=350, y=150 | - | Status (e.g., "External control") | ✅ |
| `box_ac_loads` | Rectangle | x=600, y=100 | w=150, h=100 | AC Loads box | Static |
| `txt_ac_label` | Text | x=610, y=110 | - | "⏻ AC Loads" | Static |
| `txt_ac_power` | Text | x=610, y=150 | - | AC consumption (e.g., "11W") | ✅ |

**Power Sinks/Storage (Bottom Row):**

| Component | Type | Position | Size | Purpose | ESPHome Update |
|-----------|------|----------|------|---------|----------------|
| `box_solar` | Rectangle | x=120, y=260 | w=150, h=120 | Solar yield box | Static |
| `txt_solar_label` | Text | x=130, y=270 | - | "☀ Solar yield" | Static |
| `txt_solar_power` | Text | x=130, y=320 | - | Solar power (e.g., "0W") | ✅ |
| `box_battery` | Rectangle | x=330, y=240 | w=220, h=160 | Battery box (LARGE, emphasized) | Static, Blue bg |
| `txt_battery_label` | Text | x=350, y=250 | - | "🔋 Battery" | Static |
| `txt_battery_temp` | Text | x=500, y=250 | - | Temperature (e.g., "3°C") | ✅ |
| `txt_battery_soc` | Text | x=380, y=290 | Font 4 | SOC percentage (e.g., "100%") | ✅ (with color) |
| `txt_battery_status` | Text | x=380, y=340 | - | Status (e.g., "Charging") | ✅ |
| `txt_battery_volt` | Text | x=350, y=370 | - | Voltage (e.g., "14.10V") | ✅ |
| `txt_battery_current` | Text | x=420, y=370 | - | Current (e.g., "0.4A") | ✅ |
| `txt_battery_power` | Text | x=490, y=370 | - | Power (e.g., "6W") | ✅ |
| `box_dc_loads` | Rectangle | x=600, y=260 | w=150, h=120 | DC Loads box | Static |
| `txt_dc_label` | Text | x=610, y=270 | - | "⚡ DC Loads" | Static |
| `txt_dc_power` | Text | x=610, y=320 | - | DC consumption (e.g., "65W") | ✅ |

**Flow Lines:**

| Component | Type | Purpose |
|-----------|------|---------|
| `line_shore_inv` | Line | Shore → Inverter flow indicator |
| `line_inv_ac` | Line | Inverter → AC Loads flow indicator |
| `line_solar_bat` | Line | Solar → Battery flow indicator |
| `line_inv_bat` | Line | Inverter ↔ Battery flow (bidirectional) |
| `line_bat_dc` | Line | Battery → DC Loads flow indicator |

**Design Notes:**
- Battery box is larger and blue-highlighted (most important component)
- Use Nextion line draw commands or image overlays for flow lines
- Update flow line colors based on power direction (e.g., blue=charging, yellow=discharging)
- All power values updated in real-time from ESPHome

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

**Levels Section (Pages 3-4) - Detailed Layout:**

The Levels section provides comprehensive monitoring of all fluid levels in the motorhome.

**Page 3: Levels_1 - Victron-Style Vertical Tank Display**

This page displays all tank levels using Victron Venus OS inspired vertical bars.

**Tank Column Structure (5 columns):**

Each tank column consists of:
- Header rectangle (colored) with icon and label
- Large vertical bar with 4-5 segments showing fill level
- Large percentage text at bottom
- Small actual/max value text
- Warning icon when critical

| Component | Type | Position | Size | Purpose | ESPHome Update |
|-----------|------|----------|------|---------|----------------|
| **Fresh Water Column** |||||
| `fresh_header` | Rectangle | x=120, y=100 | w=100, h=40 | Cyan header | Static |
| `fresh_icon` | Text/Image | x=155, y=110 | - | 💧 water drop icon | Static |
| `fresh_label` | Text | x=140, y=115 | - | "Fresh" | Static |
| `fresh_bar_bg` | Rectangle | x=135, y=150 | w=70, h=200 | Tank bar background (dark) | Static |
| `fresh_bar_fill` | Progress/Gauge | x=137, y=152 | w=66, h=196 | Filled portion (red if low, blue if OK) | ✅ |
| `fresh_warning` | Text | x=160, y=200 | - | "⚠" warning triangle | ✅ (visible if <20%) |
| `fresh_percent` | Text | x=150, y=360 | Font 4 | "0%" (large) | ✅ |
| `fresh_values` | Text | x=140, y=400 | Font 1 | "0/120ℓ" (small) | ✅ |
| **Waste Water Column** |||||
| `waste_header` | Rectangle | x=240, y=100 | w=100, h=40 | Cyan header | Static |
| `waste_icon` | Text/Image | x=275, y=110 | - | 💧 water drop icon | Static |
| `waste_label` | Text | x=260, y=115 | - | "Waste" | Static |
| `waste_bar_bg` | Rectangle | x=255, y=150 | w=70, h=200 | Tank bar background | Static |
| `waste_bar_fill` | Progress/Gauge | x=257, y=152 | w=66, h=196 | Filled portion (red if full, blue if OK) | ✅ |
| `waste_percent` | Text | x=270, y=360 | Font 4 | "9%" (large) | ✅ |
| `waste_values` | Text | x=260, y=400 | Font 1 | "8/80ℓ" (small) | ✅ |
| **Diesel Column** |||||
| `diesel_header` | Rectangle | x=360, y=100 | w=100, h=40 | Yellow header | Static |
| `diesel_icon` | Text/Image | x=390, y=110 | - | ⛽ fuel pump icon | Static |
| `diesel_label` | Text | x=370, y=115 | - | "FRAM-Diesel" | Static |
| `diesel_bar_bg` | Rectangle | x=375, y=150 | w=70, h=200 | Tank bar background | Static |
| `diesel_bar_fill` | Progress/Gauge | x=377, y=152 | w=66, h=196 | Filled portion (blue) | ✅ |
| `diesel_percent` | Text | x=390, y=360 | Font 4 | "73%" (large) | ✅ |
| `diesel_values` | Text | x=375, y=400 | Font 1 | "67/92ℓ" (small) | ✅ |
| **Gas 1 Column** |||||
| `gas1_header` | Rectangle | x=480, y=100 | w=100, h=40 | Purple header | Static |
| `gas1_icon` | Text/Image | x=515, y=110 | - | 🔥 flame icon | Static |
| `gas1_label` | Text | x=505, y=115 | - | "Gas 1" | Static |
| `gas1_bar_bg` | Rectangle | x=495, y=150 | w=70, h=200 | Tank bar background | Static |
| `gas1_bar_fill` | Progress/Gauge | x=497, y=152 | w=66, h=196 | Filled portion (blue) | ✅ |
| `gas1_percent` | Text | x=510, y=360 | Font 4 | "100%" (large) | ✅ |
| `gas1_values` | Text | x=495, y=400 | Font 1 | "20/20ℓ" (small) | ✅ |
| **Gas 2 Column** |||||
| `gas2_header` | Rectangle | x=600, y=100 | w=100, h=40 | Purple header | Static |
| `gas2_icon` | Text/Image | x=635, y=110 | - | 🔥 flame icon | Static |
| `gas2_label` | Text | x=625, y=115 | - | "Gas 2" | Static |
| `gas2_bar_bg` | Rectangle | x=615, y=150 | w=70, h=200 | Tank bar background | Static |
| `gas2_bar_fill` | Progress/Gauge | x=617, y=152 | w=66, h=196 | Filled portion (blue) | ✅ |
| `gas2_percent` | Text | x=630, y=360 | Font 4 | "100%" (large) | ✅ |
| `gas2_values` | Text | x=615, y=400 | Font 1 | "20/20ℓ" (small) | ✅ |
| **Control Button** |||||
| `btn_pump` | Button | x=720, y=380 | w=60, h=40 | Water pump ON/OFF | ✅ (bidirectional) |

**Design Philosophy:**
- Victron Venus OS inspired vertical tank bars
- 5 columns for all fluid types at a glance
- Segmented bars (4-5 horizontal lines for visual segments)
- Color-coded headers (Cyan for water, Yellow for diesel, Purple for gas)
- Warning triangles appear automatically when levels critical
- Large percentage display for easy reading
- Water pump control button accessible on same page

**Page 4: Levels_2 - External Fill Display**

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

**Power Section (Pages 9-10):**

**Page 9: Power_1 - SmartEBL Relay Controls**

This page provides direct control of all SmartEBL relay switches.

| Component | Type | Position | Size | Purpose | ESPHome Update |
|-----------|------|----------|------|---------|----------------|
| **Light Relay Button** |||||
| `btn_relay_light` | Button/Dual-state | x=150, y=120 | w=120, h=100 | Light relay control | ✅ (bidirectional) |
| `icon_light` | Text/Image | x=195, y=135 | - | 💡 light bulb icon | Static |
| `label_light` | Text | x=185, y=170 | - | "Light" | Static |
| `status_light` | Text | x=185, y=195 | - | "ON" or "OFF" | ✅ (color coded) |
| **User Relay Button** |||||
| `btn_relay_user` | Button/Dual-state | x=290, y=120 | w=120, h=100 | User-configurable relay | ✅ (bidirectional) |
| `icon_user` | Text/Image | x=335, y=135 | - | 👤 user icon | Static |
| `label_user` | Text | x=325, y=170 | - | "User" | Static |
| `status_user` | Text | x=325, y=195 | - | "ON" or "OFF" | ✅ (color coded) |
| **Aux Relay Button** |||||
| `btn_relay_aux` | Button/Dual-state | x=430, y=120 | w=120, h=100 | Auxiliary power relay | ✅ (bidirectional) |
| `icon_aux` | Text/Image | x=475, y=135 | - | ⚡ lightning icon | Static |
| `label_aux` | Text | x=465, y=170 | - | "Aux" | Static |
| `status_aux` | Text | x=465, y=195 | - | "ON" or "OFF" | ✅ (color coded) |
| **Pump Relay Button** |||||
| `btn_relay_pump` | Button/Dual-state | x=220, y=250 | w=120, h=100 | Water pump relay | ✅ (bidirectional) |
| `icon_pump` | Text/Image | x=265, y=265 | - | 💧 water drop icon | Static |
| `label_pump` | Text | x=255, y=300 | - | "Pump" | Static |
| `status_pump` | Text | x=255, y=325 | - | "ON" or "OFF" | ✅ (color coded) |
| **Fridge Relay Button** |||||
| `btn_relay_fridge` | Button/Dual-state | x=360, y=250 | w=120, h=100 | Fridge power relay | ✅ (bidirectional) |
| `icon_fridge` | Text/Image | x=405, y=265 | - | ❄️ snowflake icon | Static |
| `label_fridge` | Text | x=390, y=300 | - | "Fridge" | Static |
| `status_fridge` | Text | x=390, y=325 | - | "ON" or "OFF" | ✅ (color coded) |

**Button States:**
- **ON**: Green background (2016), white text
- **OFF**: Red background (63488) or dark gray, white text

**Touch Events:**
- Press button to toggle relay state
- ESPHome receives command and updates relay
- ESPHome sends current state back to display
- Display updates button appearance based on actual relay state

**Design Philosophy:**
- Large touch-friendly buttons (120x100px)
- Clear visual feedback (color coded)
- Icon + Label + Status for each relay
- Bidirectional control (can be toggled from HA or display)
- Real-time state synchronization

**Page 10: Power_2 - Energy Monitoring**

Additional power monitoring page (battery history, energy consumption charts, etc.)

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

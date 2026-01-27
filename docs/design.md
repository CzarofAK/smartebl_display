# Design Guide

Complete design guide for SmartEBL Display - philosophy, principles, mockups, and layout specifications.

---

## Table of Contents

1. [Design Philosophy](#design-philosophy)
2. [Display Mockups](#display-mockups)
3. [Transparent Design](#transparent-design)
4. [Color System](#color-system)
5. [Layout Principles](#layout-principles)
6. [Typography](#typography)
7. [Component Sizes](#component-sizes)
8. [Navigation Patterns](#navigation-patterns)
9. [Icons & Symbols](#icons--symbols)
10. [Responsive Design](#responsive-design)
11. [Accessibility](#accessibility)
12. [Common Mistakes](#common-mistakes)

---

## Design Philosophy

**Core Principles:**

✅ **Clarity** - Information readable at a glance
✅ **Prioritization** - Critical information first (Master Warning/Caution)
✅ **Consistency** - Same elements always in same positions
✅ **Minimalism** - Only relevant information, no clutter
✅ **Fault Tolerance** - Unambiguous operation, no confusion

### Design Goals

1. **Easy Readable** - Large fonts, high contrast
2. **Quick status check** - Master Warning text at top
3. **Easy navigation** - Touch targets sized for fingers
4. **Professional appearance** - Clean, transparent design
5. **Customizable** - Works with any background

---

## Display Mockups

Visual representation of all display pages (7" - 800x480px).

### Home Screen (Page 0)

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  ✓ ALL SYSTEMS NORMAL                                              │
│                                                                     │
├───────────────┬─────────────────────────────────────────────────────┤
│               │                                                     │
│   Electric    │                                                     │
│               │                                                     │
│               │              MAIN MENU                              │
│   Water       │                                                     │
│               │         SmartEBL Display System                     │
│               │                                                     │
│   Climate     │      Select a section from the menu                 │
│               │                                                     │
│               │                                                     │
│   Status      │                                                     │
│               │                                                     │
│               │                                                     │
│   Power       │                                                     │
│               │                                                     │
└───────────────┴─────────────────────────────────────────────────────┘
```

**Key Features:**
- Master Warning text at top (when no warnings acitve, invisible)
- 5 menu buttons on left (150px wide)
- Large content area for welcome message
- Clean, minimal design

---

### Electric Page 1 (Page 1)

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                1/2  │
│  ✓ ALL SYSTEMS NORMAL                                              │
│                                                                     │
├───────────────┬─────────────────────────────────────────────────────┤
│               │                                                     │
│ ━━Electric━━  │   BATTERY STATUS                                    │
│               │                                                     │
│   Water       │   Voltage:     12.8V                                │
│               │   Current:      5.2A                                │
│   Climate     │   SOC:          78%  🟢                             │
│               │                                                     │
│   Status      │   SOLAR                                             │
│               │   Power:       245W                                 │
│   Power       │   Status:      Charging  🟢                         │
│               │                                                     │
│               │   SHORE POWER                                       │
│               │   Status:      Disconnected                         │
│               │                                                     │
│               │                                                     │
└───────────────┴─────────────────────────────────────────────────────┘
```

**Key Features:**
- Active button highlighted (━━Electric━━ in green with underline)
- Page counter (1/2) top right
- Battery status with color-coded SOC
- Change to next page with repeated press on "Electric"

---

### Status Page 1 - Fuses Overview (Page 7)

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                1/2  │
│  ⚠️ MASTER WARNING                                                  │
│                                                                     │
├───────────────┬─────────────────────────────────────────────────────┤
│               │                                                     │
│   Electric    │   FUSES                                             │
│               │                                                     │
│   Water       │   🔴 Fuse.1 Main      🟢 Fuse.2 Lights             │
│               │   🟢 Fuse.3 Water     🟢 Fuse.4 Heater             │
│   Climate     │   🔴 Fuse.5 12V Aux   🟢 Fuse.6 Fridge             │
│               │   🟢 Fuse.7 Pump      🟢 Fuse.8 Outlet             │
│ ━━Status━━    │   🟢 Fuse.9 Climate   🟢 Fuse.10 USB              │
│               │                                                     │
│   Power       │   ELECTRICAL STATUS                                 │
│               │   Battery:    12.8V (78%) 🟢                        │
│               │   Solar:      245W        🟢                        │
│               │   Shore:      Disconnected                          │
│               │                                                     │
│               │                                                     │
└───────────────┴─────────────────────────────────────────────────────┘
```

**Key Features:**
- Master Warning in red (⚠️ MASTER WARNING)
- Grid layout for 18 fuses (3 columns)
- Color-coded status (🔴 = blown, 🟢 = OK)
- Quick electrical summary

---

### Master Warning States

#### Normal State (invisible)
```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

#### Master Caution (Orange)
```
┌─────────────────────────────────────────────────────────────────────┐
│  ⚡ MASTER CAUTION                                                  │
└─────────────────────────────────────────────────────────────────────┘
```

#### Master Warning (Red)
```
┌─────────────────────────────────────────────────────────────────────┐
│  ⚠️ MASTER WARNING                                                  │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Navigation Flow Diagram

```
                          HOME (Page 0)
                               │
        ┌──────────────┬───────┼───────┬──────────────┐
        │              │       │       │              │
    ELECTRIC        WATER   CLIMATE  STATUS        POWER
   (Pages 1-2)   (Pages 3-4) (5-6)  (Pages 7-8)  (Pages 9-10)
        │              │       │       │              │
        ↓              ↓       ↓       ↓              ↓
    Electric_1     Water_1  Climate_1 Status_1    Power_1
        │              │       │       │              │
        ↓              ↓       ↓       ↓              ↓
    Electric_2     Water_2  Climate_2 Status_2    Power_2
        │              │       │       │              │
        └──────────────┴───────┴───────┴──────────────┘
                          │
                     Back to 1
                   (or to other
                     section)
```

---

### Button States

#### Inactive Menu Button
```
┌───────────────┐
│               │
│   Electric    │  ← White (65535)
│               │
└───────────────┘
```

#### Active Menu Button (Current Section)
```
┌───────────────┐
│               │
│ ━━Electric━━  │  ← Green (2016) + Underline
│               │
└───────────────┘
```

---

## Transparent Design

### Why Transparent?

- **Flexible**: Works with any background/wallpaper
- **Modern**: Follows glassmorphism design trend
- **Resource-efficient**: No large images needed
- **Customizable**: Users can add their own backgrounds
- **Professional**: Clean, uncluttered appearance

### Implementation

**All Components:**
```
bco=65535    // Background Color = Transparent
pco=65535    // Text Color = White (default)
pco2=65535   // Press Color 2 = Transparent
```

**Result**: Only text visible, no "button frames" or backgrounds

---

## Color System

### Primary Colors

| Color | Hex | Decimal | RGB | Usage |
|-------|-----|---------|-----|-------|
| 🔴 Red | `#F800` | 63488 | (248,0,0) | Master WARNING, critical alarms |
| 🟠 Orange | `#FC00` | 64512 | (252,0,0) | Master CAUTION, warnings |
| 🟢 Green | `#07E0` | 2016 | (0,124,0) | Normal, OK, Active |
| ⚪ White | `#FFFF` | 65535 | (255,255,255) | Text, Inactive |
| 🔘 Gray | `#C618` | 50712 | (198,24,192) | Disabled |

### Color Logic

**Buttons:**
- White (65535): Default / Inactive
- Green (2016): Active section
- Underline: Visual indicator for active

**Alarms:**
- Orange (64512): Warning (Caution) - attention needed
- Red (63488): Critical (Warning) - action required

**Scrolling Text Examples:**
```
Orange: "⚡ MASTER CAUTION"
Red:    "⚠️ MASTER WARNING"
```

### RGB to Nextion Decimal Conversion

**Formula:**
```
Decimal = (R << 11) | (G << 5) | B

where:
R = 0-31 (5 bit)
G = 0-63 (6 bit)
B = 0-31 (5 bit)
```

**Example: Blue (0, 0, 255)**
```
R=0, G=0, B=31
Decimal = (0 << 11) | (0 << 5) | 31 = 31
```

**Online Tool**: [https://nextion.tech/color-converter/](https://nextion.tech/color-converter/)

---

## Layout Principles

### Grid System (800x480)

```
┌────────────────────────────────────────────────────────┐
│ 0─────────────────── Header (50px) ─────────────────800│
│                                                         │
│ 0  Menu   │                                             │
│    (150)  │         Content Area                        │
│           │         (650x430)                           │
│           │                                             │
│           │                                             │
│           │                                             │
│           │                                             │
│           │                                             │
│ 480       │                                             │
└───────────┴─────────────────────────────────────────────┘
    0     150                                           800
```

### Layout Zones

**1. Header Zone (y=0-50)**
- Scrolling text (Master Warning/Caution) - left
- Page counter - right (x=700)
- Height: 50px
- Always visible on all pages

**2. Menu Zone (x=0-150, y=50-480)**
- 5 vertical buttons
- Each button: 80px high, 10px spacing
- Width: 150px
- Persistent across all pages

**3. Content Zone (x=150-800, y=50-480)**
- Freely designable area
- Size: 650 x 430 px
- Recommended: 20px padding from edges
- Page-specific content

### Detailed Grid

```
    0        150                                                   800
    ├─────────┼────────────────────────────────────────────────────┤
    │         │                                                    │  0
    │  MENU   │              HEADER (Alert Text)                   │
    │         │                                                    │  50
    ├─────────┼────────────────────────────────────────────────────┤
    │         │                                                    │
    │         │                                                    │
    │ Button  │                                                    │
    │ Button  │                                                    │
    │ Button  │            CONTENT AREA                            │
    │ Button  │            (650 x 430 px)                          │
    │ Button  │                                                    │
    │         │                                                    │
    │         │                                                    │
    │         │                                                    │
    │         │                                                    │  480
    └─────────┴────────────────────────────────────────────────────┘

Menu Width:    150px
Content Width: 650px
Header Height: 50px
Content Height: 430px
```

---

## Typography

### Font Hierarchy

| Font ID | Size | Usage | Example |
|---------|------|-------|---------|
| Font 0 | 12pt | Fine print, labels | "Voltage:" |
| Font 1 | 16pt | Standard text, values | "12.8V" |
| Font 2 | 20pt | Buttons, important text | "Electric" |
| Font 3 | 24pt | Scrolling text, headings | "MASTER WARNING" |
| Font 4 | 32pt | Large values, numbers | "78%" |

### Font Recommendations

**In Nextion Editor:**
1. File → Font Generator
2. Choose Font: **Roboto** or **Arial** (highly readable)
3. Create sizes: 12, 16, 20, 24, 32pt
4. **Anti-Aliasing**: Enabled (for smooth edges)
5. **Format**: 8-bit for color support

### Typography Best Practices

- **Hierarchy**: Use size to show importance
- **Consistency**: Same size for same type (all values = Font 1)
- **Readability**: Minimum 16pt for daylight readability
- **Contrast**: White on dark background = optimal

---

## Component Sizes

### Buttons

**Menu Buttons (vertical):**
- Size: w=150, h=80
- Spacing: 10px vertical
- Touch-friendly for fingers

**Action Buttons (e.g., "Next"):**
- Size: w=120, h=50
- Position: Bottom right (x=650, y=400)
- Clear call to action

**Touch Area Guidelines:**
- Minimum: 60x60 px (for reliable touch)
- Recommended: 80x80 px or larger
- Menu buttons: 150x80 px (very comfortable)

### Text Components

**Scrolling Text (Master Warning):**
- Height: 50px (for 24pt Font + padding)
- Width: 800px (full width) or 650px (with page counter)
- Scrolls when text > width

**Value Display:**
- Height: 40-50px
- Width: As needed for content
- Font: Font 1 or Font 2

**Labels:**
- Height: 30px
- Font: Font 0 or Font 1
- Compact, descriptive

### Touch Areas Summary

```
Menu Buttons: 150x80 px (easily accessible)
Next Button:  120x50 px (bottom right)
Alert Text:   800x50 px (top, clickable for status if configured)
```

---

## Navigation Patterns

### Horizontal Navigation (Between Sections)

```
Electric → Water → Climate → Status → Power
   ↓         ↓         ↓         ↓       ↓
Electric_1 Water_1  Climate_1  Status_1 Power_1
```

**Always via menu buttons on the left**

### Vertical Navigation (Within Section)

```
Electric_1
    ↓ (btn_next or btn_electric)
Electric_2
    ↓ (btn_next or btn_electric)
Electric_1 (loops back)
```

**Via:**
- "Next →" button (explicit)
- Clicking active section button (toggle through pages)

---

## Icons & Symbols

### Unicode Symbols (Work in Nextion)

```
✓ Check Mark (OK)         U+2713
⚠️ Warning                U+26A0
⚡ Caution/Lightning      U+26A1
🔴 Red Circle            U+1F534
🟢 Green Circle          U+1F7E2
🟠 Orange Circle         U+1F7E0
→ Arrow Right            U+2192
↓ Arrow Down             U+2193
```

### Usage Examples

```c
// Nextion
txt_alert.txt="✓ ALL SYSTEMS NORMAL"
txt_alert.txt="⚠️ MASTER WARNING"
txt_status.txt="🟢 OK"
txt_fuse.txt="🔴 Fuse.1 Main"
```

**Tip**: Test symbols in Nextion Editor to ensure font supports them\!

---

## Accessibility

### Readability

✅ **Contrast**: White on black/dark = optimal for daylight
✅ **Font Size**: Minimum 16pt for readability while driving
✅ **Touch Targets**: Minimum 60x60px for finger operation
✅ **Color Coding**: Always combine with text (not color alone)

### Color Blindness Considerations

**Protanopia/Deuteranopia (Red-Green Weakness):**

**Problem**: Red and green not easily distinguishable

**Solution:**
- Use symbols + color: "⚠️ 🔴" not just color
- Text labels: "WARNING" + red color
- Multiple indicators: Color + icon + text

**Example:**
```
Bad:  ⚠️ (only red color)
Good: ⚠️ MASTER WARNING (red color + text)
```

---

## Animation & Feedback

### Touch Feedback

**Standard Approach:**
- Press Color (pco2) = Same as pco (no visual feedback)
- Feedback via page change (navigation)

**Alternative:**
- pco2 = Slightly lighter color for subtle feedback
- Sound feedback via `click` command in Touch Event

### Scrolling Text

```
dis=1        // Scrolling enabled
tim=50       // Speed (50 = medium, lower = faster)
dir=0        // Left-to-right
```

**Best Practice:**
- Use scrolling ONLY for alert text
- Don't scroll normal values (confusing)
- Test scroll speed on actual hardware

---

## Performance Tips

### Optimization

**Fewer Updates = Better Performance**

❌ **Bad:**
```c
// Updating every second even if unchanged
// In timer (every 1000ms)
txt_value.txt="12.8"
```

✅ **Good:**
```c
// Only on change (via ESPHome on_value)
if(new_value\!=old_value)
{
  txt_value.txt=new_value
}
```

### Avoid Large Images

**Transparent Design Benefits:**
- No large background images needed
- Faster page load times
- Less flash memory usage
- Easier updates

---

## Common Mistakes

### ❌ Mistake 1: Too Much on One Page

**Problem**: User overwhelmed with 20+ values on single page

**Solution**: 
- Split into multiple subpages
- Group logically (e.g., Battery on page 1, Solar on page 2)
- Max 6-8 values per page

---

### ❌ Mistake 2: Inconsistent Colors

**Problem**: Red sometimes means "Alarm", sometimes "Active"

**Solution**:
- Stick to color scheme:
  - Red = Critical/Warning
  - Orange = Caution
  - Green = OK/Active/Normal
  - White = Neutral/Inactive

---

### ❌ Mistake 3: Touch Targets Too Small

**Problem**: Hard to hit buttons while driving

**Solution**:
- Minimum: 60x60 px
- Recommended: 80x80 px or larger
- Test on actual hardware before finalizing

---

### ❌ Mistake 4: Missing Feedback

**Problem**: User doesn't know if touch registered

**Solution**:
- Page change = confirmation
- Or add sound feedback
- Or subtle color change (pco2)

---

## Page Templates

### Template 1: Standard Information Page

```
┌────────────────────────────────────────────────────────┐
│ ✓ ALL SYSTEMS NORMAL                           1/2     │
├───────────┬─────────────────────────────────────────────┤
│           │                                             │
│ Electric  │  [SECTION TITLE]                            │
│           │                                             │
│ Water     │  Label1: Value1                             │
│           │  Label2: Value2                             │
│ Climate   │  Label3: Value3                             │
│           │                                             │
│ Status    │                                             │
│           │                          [Next →]           │
│ Power     │                                             │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

**Use for**: Electric, Water, Climate, Power sections

---

### Template 2: Status Grid Overview

```
┌────────────────────────────────────────────────────────┐
│ ⚠️ MASTER WARNING                              1/2     │
├───────────┬─────────────────────────────────────────────┤
│           │  🔴 Item.1   🟢 Item.2   🟢 Item.3         │
│ Electric  │  🟢 Item.4   🔴 Item.5   🟢 Item.6         │
│           │  🟢 Item.7   🟢 Item.8   🟢 Item.9         │
│ Water     │                                             │
│           │  STATUS SUMMARY                             │
│ Climate   │  Field1: Value1  🟢                         │
│           │  Field2: Value2  🟢                         │
│ Status    │  Field3: Value3  🔴                         │
│           │                          [Next →]           │
│ Power     │                                             │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

**Use for**: Status section (fuses, system overview)

---

## Design Workflow

### Recommended Sequence

1. **Wireframe** (Paper/Whiteboard)
   - Sketch layout
   - Define what goes on each page
   - Plan navigation flow

2. **Prototype** (Nextion Editor)
   - Create Page 0 (Home) perfectly
   - Test navigation concept
   - Verify touch areas

3. **Create Template**
   - Perfect one section page (e.g., Electric_1)
   - Copy as template for other pages
   - Adjust content only

4. **Iterate**
   - Fill all pages with content
   - Test navigation between all pages
   - Test on real hardware

5. **ESPHome Integration**
   - Connect with real sensor data
   - Test dynamic updates
   - Verify color changes

6. **Feedback & Refine**
   - Use in real environment
   - Adjust based on usage
   - Fine-tune layouts

---

## Consistency Checklist

When creating new pages, ensure:

- [ ] Scrolling text at same position (y=0)
- [ ] Menu buttons at same position (x=0, y=50-480)
- [ ] Page counter at same position (x=700, y=10)
- [ ] Same fonts for same element types
- [ ] Transparent backgrounds (bco=65535)
- [ ] Touch events follow same pattern
- [ ] Page Preinit sets button highlighting correctly
- [ ] Color scheme followed (Green/Orange/Red)

---

## Tools & Resources

- **Nextion Editor**: [Download](https://nextion.tech/nextion-editor/)
- **Color Calculator**: [Online Tool](https://nextion.tech/color-converter/)
- **Instruction Set**: [Nextion Docs](https://nextion.tech/instruction-set/)
- **Font Generator**: Built into Nextion Editor (File → Font Generator)
- **ESPHome Nextion**: [Documentation](https://esphome.io/components/display/nextion.html)

---

## Summary

**5 Core Design Principles:**

1. 🎨 **Transparent & Minimalist** - Clean, professional appearance
2. 🚨 **Clear Alarm Hierarchy** - Master Warning/Caution system
3. 🎯 **Consistent Navigation** - Predictable, easy to learn
4. 📏 **Grid-based Layout** - Organized, structured information
5. ♿ **Accessibility First** - Readable, touch-friendly, inclusive

**Follow these principles → Professional, usable display\!**

---

## Next Steps

- **[Nextion Guide](../nextion/nextion.md)** - Create HMI pages
- **[Installation Guide](installation.md)** - Install the system
- **[ESPHome Guide](../esphome/esphome.md)** - Configure sensors

---

**Design with purpose\! 🎨**
DESIGN_EOF < /dev/null

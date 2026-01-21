# Nextion Design Guide

**Design Principles and Best Practices for SmartEBL Display**

---

## Design Philosophy

### Inspiration: Aviation Cockpits

The design is inspired by **Boeing/Airbus cockpit systems**:

✅ **Clarity** - Information at a glance
✅ **Prioritization** - Important things first (Master Warning/Caution)
✅ **Consistency** - Same elements, same positions
✅ **Minimalism** - Only relevant information
✅ **Fault Tolerance** - Unambiguous operation

---

## Transparent Design

### Why Transparent?

- **Flexible**: Works with any background/wallpaper
- **Modern**: Glassmorphism trend
- **Resource-efficient**: No large images needed
- **Customizable**: Users can use their own backgrounds

### Implementation

**All Components:**
```
bco=65535    // Background Color = Transparent
pco=65535    // Text Color = White (default)
pco2=65535   // Press Color 2 = Transparent
```

**Only text visible, no "button frame"**

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

**Alarms:**
- Green (2016): OK
- Orange (64512): Warning (Caution)
- Red (63488): Critical (Warning)

**Scrolling Text:**
- Green: "✓ ALL SYSTEMS NORMAL"
- Orange: "⚡ MASTER CAUTION"
- Red: "⚠️ MASTER WARNING"

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

### Zones

1. **Header (y=0-50)**
   - Scrolling text left
   - Page counter right

2. **Menu (x=0-150, y=50-480)**
   - 5 buttons vertically
   - Each 80px high, 10px spacing

3. **Content (x=150-800, y=50-480)**
   - Freely designable
   - Recommended: 20px padding

---

## Typography

### Font Hierarchy

| Font ID | Size | Usage | Example |
|---------|------|-------|---------|
| Font 0 | 12pt | Fine print, labels | "Voltage:" |
| Font 1 | 16pt | Standard text | "12.8V" |
| Font 2 | 20pt | Buttons, important text | "Electric" |
| Font 3 | 24pt | Scrolling text, headings | "MASTER WARNING" |
| Font 4 | 32pt | Large values, numbers | "78%" |

### Font Recommendations

**In Nextion Editor:**
1. File → Font Generator
2. Choose Font: **Roboto** or **Arial** (highly readable)
3. Create sizes: 12, 16, 20, 24, 32pt
4. **Anti-Aliasing**: Enabled (for smooth edges)
5. **Format**: 8-bit for color

---

## Component Sizes

### Buttons

**Menu Buttons (vertical):**
- w=150, h=80
- Spacing: 10px

**Action Buttons (e.g. "Next"):**
- w=120, h=50
- Position: Bottom right (x=650, y=400)

**Touch Area**: Minimum 60x60px for good usability

### Text Components

**Scrolling Text:**
- h=50px (for 24pt Font + Padding)
- Full width (w=800) or with space for page counter (w=650)

**Value Display:**
- h=40-50px
- w=as needed

**Labels:**
- h=30px
- Font 0 or 1

---

## Navigation Pattern

### Horizontal Navigation (between Sections)

```
Electric → Water → Climate → Status → Power
   ↓         ↓         ↓         ↓       ↓
Electric_1 Water_1  Climate_1  Status_1 Power_1
```

**Always via Menu buttons on the left**

### Vertical Navigation (within Section)

```
Electric_1
    ↓ (btn_next)
Electric_2
    ↓ (btn_next or btn_electric)
Electric_1
```

**Via:**
- "Next →" button
- Clicking section button again

---

## Page Templates

### Template 1: Standard Subpage

```
┌────────────────────────────────────────────────────────┐
│ ✓ ALL SYSTEMS NORMAL                           1/2     │
├───────────┬─────────────────────────────────────────────┤
│           │                                             │
│ Electric  │  [TITLE]                                    │
│           │                                             │
│ Water     │  Label1: Value1                             │
│           │  Label2: Value2                             │
│ Climate   │  Label3: Value3                             │
│           │                                             │
│ Status    │                                             │
│           │                                             │
│ Power     │                          [Next →]           │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

### Template 2: Status Overview (Grid)

```
┌────────────────────────────────────────────────────────┐
│ ⚠️ MASTER WARNING                              1/2     │
├───────────┬─────────────────────────────────────────────┤
│           │  🔴 Fuse.1   🟢 Fuse.2   🟢 Fuse.3         │
│ Electric  │  🟢 Fuse.4   🔴 Fuse.5   🟢 Fuse.6         │
│           │  🟢 Fuse.7   🟢 Fuse.8   🟢 Fuse.9         │
│ Water     │                                             │
│           │  TANK LEVELS                                │
│ Climate   │  Fresh: 45L (60%) 🟢                        │
│           │  Gray:  28L (40%) 🟢                        │
│ Status    │  Black: 12L (20%) 🔴                        │
│           │  LPG:   3.2kg (40%) 🟢                      │
│ Power     │                          [Next →]           │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

---

## Icons & Symbols

### Unicode Symbols (work in Nextion)

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

### Usage

```
txt_alert.txt="✓ ALL SYSTEMS NORMAL"
txt_alert.txt="⚠️ MASTER WARNING"
txt_status.txt="🟢 OK"
```

**Tip**: Test in Nextion Editor if font supports the symbols!

---

## Animation & Feedback

### Touch Feedback

**Standard:**
- Press Color (pco2) = Same color as pco (no feedback)
- Or: pco2 = Slightly lighter for subtle feedback

**Alternative:**
- Sound feedback via `click` command in Touch Event

### Scrolling Text

```
dis=1        // Scrolling enabled
tim=50       // Speed (50 = medium)
dir=0        // Left-to-right
```

**Best Practice:**
- Only for scrolling text (alarms)
- Not for normal values (confuses user)

---

## Responsive Content

### Different Display Sizes

This design is for **7" (800x480)**.

**For Other Sizes:**

| Display | Resolution | Adjustments |
|---------|------------|-------------|
| 3.5" | 480x320 | Menu narrower (100px), fonts smaller |
| 5" | 800x480 | 1:1 usable |
| 7" | 800x480 | Perfect (Original) |
| 10.1" | 1024x600 | Content area larger, fonts larger |

### Scaling Factors

```
Menu Width:     800x480 → 150px  |  1024x600 → 200px
Base Font:      800x480 → 16pt   |  1024x600 → 20pt
Button Height:  800x480 → 80px   |  1024x600 → 100px
```

---

## Accessibility

### Readability

✅ **Contrast**: White on Black/Dark = Optimal
✅ **Font Size**: Min. 16pt for readability in daylight
✅ **Touch Targets**: Min. 60x60px for finger operation

### Color Blindness

**Protanopia/Deuteranopia (Red-Green Weakness):**

Problem: Red/Green not distinguishable

**Solution:**
- Additionally use symbols: ⚠️ + 🔴
- Text labels: "WARNING" instead of just color

---

## Performance Tips

### Optimization

**Fewer Updates = Better Performance**

❌ **Bad:**
```c
// In timer (every second)
txt_value.txt="12.8"
```

✅ **Good:**
```c
// Only on change (via ESPHome)
if(new_value!=old_value)
{
  txt_value.txt=new_value
}
```

### Avoid Image Files

**Transparent Design = No large background images**

→ Faster load times
→ Less flash memory

---

## Consistency Checklist

When creating new pages:

- [ ] Scrolling text at same position (y=0)
- [ ] Menu buttons at same position (x=0, y=50-480)
- [ ] Page counter at same position (x=700, y=10)
- [ ] Same fonts for same elements
- [ ] Transparent backgrounds (bco=65535)
- [ ] Touch events follow same pattern
- [ ] Page Preinit sets button highlighting

---

## Common Mistakes

### ❌ Mistake 1: Too Much on One Page

**Problem**: User overwhelmed with 20+ values on one page

**Solution**: Multiple subpages, logically grouped

---

### ❌ Mistake 2: Inconsistent Colors

**Problem**: Red sometimes means "Alarm", sometimes "Active"

**Solution**: Stick to color scheme (see above)

---

### ❌ Mistake 3: Touch Targets Too Small

**Problem**: Buttons hard to hit while driving

**Solution**: Min. 60x60px, better 80x80px

---

### ❌ Mistake 4: Missing Feedback

**Problem**: User doesn't know if touch was registered

**Solution**: Page change as feedback or sound

---

## Design Workflow

### Recommended Sequence

1. **Wireframe** (Paper/Whiteboard)
   - Sketch layout
   - Define navigation

2. **Prototype** (Nextion Editor)
   - Create Page 0 (Home)
   - Test navigation concept

3. **Create Template**
   - Perfect one subpage
   - Copy as template

4. **Iterate**
   - Fill all pages
   - Test on real display

5. **ESPHome Integration**
   - Connect with real data

6. **Feedback & Adjust**

---

## Further Resources

- **Nextion Instruction Set**: [https://nextion.tech/instruction-set/](https://nextion.tech/instruction-set/)
- **Nextion Forum**: [https://forum.nextion.tech/](https://forum.nextion.tech/)
- **ESPHome Nextion Docs**: [https://esphome.io/components/display/nextion.html](https://esphome.io/components/display/nextion.html)

---

## Summary

**5 Core Principles:**

1. 🎨 **Transparent & Minimalist**
2. 🚨 **Clear Alarm Hierarchy**
3. 🎯 **Consistent Navigation**
4. 📏 **Grid-based Layout**
5. ♿ **Consider Accessibility**

**Follow These Principles → Professional, Usable Display!**

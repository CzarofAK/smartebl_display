# Nextion Page Structure

**Complete Step-by-Step Guide for the Nextion Display**

## Display Specifications

- **Model**: NX8048P070 (7" Enhanced)
- **Resolution**: 800 x 480 pixels
- **Orientation**: Horizontal
- **Baud Rate**: 115200

---

## Page Overview

| Page ID | Name | Description |
|---------|------|-------------|
| 0-1 | Electric_1, Electric_2 | Electrical systems |
| 2-3 | Water_1, Water_2 | Water systems |
| 4-5 | Climate_1, Climate_2 | Climate & heating |
| 6-7 | Status_1, Status_2 | System status & alarms |
| 8-9 | Power_1, Power_2 | Energy & batteries |

---

## Global Variables

**Program.s → Add Variables:**

```c
// Stores current section (0=Home, 1=Electric, 2=Water, 3=Climate, 4=Status, 5=Power)
int currentSection=0

// Stores page index within section (0=first, 1=second)
int currentPage=0

// Number of pages per section (for dynamic calculation)
int electricPages=2
int waterPages=2
int climatePages=2
int statusPages=2
int powerPages=2
```

---

## Color Definitions

| Color | Hex | Decimal | Usage |
|-------|-----|---------|-------|
| Red | `#F800` | 63488 | Master WARNING |
| Orange | `#FC00` | 64512 | Master CAUTION |
| Green | `#07E0` | 2016 | Normal / Active |
| Gray | `#C618` | 50712 | Inactive |
| White | `#FFFF` | 65535 | Text / Transparent |

---

## Page 0: Home (Main Menu)

### Components

#### 1. Scrolling Text - Master Warning/Caution

- **Type**: Scrolling Text
- **Name**: `txt_alert`
- **Position**: x=0, y=0
- **Size**: w=800, h=50
- **Font**: Font 3 (24pt)
- **Properties**:
  - `bco`: 65535 (transparent)
  - `pco`: 2016 (green, changes dynamically)
  - `pco2`: 65535 (transparent)
  - `dir`: 0 (left-to-right)
  - `dis`: 1 (scrolling enabled)
  - `tim`: 50 (scroll speed)
  - `xcen`: 1 (centered)
  - `ycen`: 1 (centered vertically)

#### 2. Menu Buttons (5x vertical)

**Button: Electric**
- **Type**: Button
- **Name**: `btn_electric`
- **Position**: x=0, y=60
- **Size**: w=150, h=80
- **Text**: "Electric"
- **Font**: Font 2 (20pt)
- **Properties**:
  - `bco`: 65535 (transparent)
  - `pco`: 65535 (white)
  - `pco2`: 65535 (transparent)
  - `txt`: "Electric"

**Touch Release Event:**
```c
currentSection=1
currentPage=0
page Electric_1
```

---

**Button: Water**
- **Name**: `btn_water`
- **Position**: x=0, y=150
- **Size**: w=150, h=80
- **Text**: "Water"

**Touch Release Event:**
```c
currentSection=2
currentPage=0
page Water_1
```

---

**Button: Climate**
- **Name**: `btn_climate`
- **Position**: x=0, y=240
- **Size**: w=150, h=80
- **Text**: "Climate"

**Touch Release Event:**
```c
currentSection=3
currentPage=0
page Climate_1
```

---

**Button: Status**
- **Name**: `btn_status`
- **Position**: x=0, y=330
- **Size**: w=150, h=80
- **Text**: "Status"

**Touch Release Event:**
```c
currentSection=4
currentPage=0
page Status_1
```

---

**Button: Power**
- **Name**: `btn_power`
- **Position**: x=0, y=420
- **Size**: w=150, h=80
- **Text**: "Power"

**Touch Release Event:**
```c
currentSection=5
currentPage=0
page Power_1
```

---

### Page Preinit Event (Home)

```c
// Set all buttons to standard color
btn_electric.pco=65535
btn_water.pco=65535
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Reset section tracking
currentSection=0
currentPage=0
```

---

## Pages 1-2: Electric Section

### Page 1: Electric_1

#### Components

**1. Scrolling Text** (same as Home - copy)
- Name: `txt_alert`

**2. Page Counter**
- **Type**: Text
- **Name**: `txt_page_counter`
- **Position**: x=700, y=10
- **Size**: w=80, h=30
- **Font**: Font 1 (16pt)
- **Text**: "1/2"
- **Properties**:
  - `bco`: 65535 (transparent)
  - `pco`: 65535 (white)
  - `txt`: "1/2"
  - `xcen`: 2 (right aligned)
  - `ycen`: 1 (centered)

**3. Menu Buttons** (same as Home - copy)
- `btn_electric`, `btn_water`, `btn_climate`, `btn_status`, `btn_power`

**Touch Events for Menu Buttons:**

```c
// btn_electric - Next Electric page
currentPage++
if(currentPage>=electricPages)
{
  currentPage=0
}
page Electric_2

// btn_water - To Water section
currentSection=2
currentPage=0
page Water_1

// btn_climate - To Climate section
currentSection=3
currentPage=0
page Climate_1

// btn_status - To Status section
currentSection=4
currentPage=0
page Status_1

// btn_power - To Power section
currentSection=5
currentPage=0
page Power_1
```

**4. Next Page Button**
- **Type**: Button
- **Name**: `btn_next`
- **Position**: x=650, y=400
- **Size**: w=120, h=50
- **Text**: "Next →"

**Touch Release Event:**
```c
currentPage=1
page Electric_2
```

**5. Content Area** (Placeholder for your components)
- Here add your individual elements:
  - Text for battery voltage
  - Number for current
  - Progress bar for SOC
  - etc.

---

#### Page Preinit Event (Electric_1)

```c
// Highlight active button
btn_electric.pco=2016  // Green
btn_water.pco=65535    // White
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Update page counter
txt_page_counter.txt="1/2"

// Update tracking
currentSection=1
currentPage=0
```

---

### Page 2: Electric_2

**Identical structure as Electric_1, but:**

**Adjust touch events:**

```c
// btn_electric - Back to Electric_1
currentPage=0
page Electric_1

// btn_next - Back to Electric_1 or continue (if 3rd page exists)
currentPage=0
page Electric_1
```

**Page Preinit Event:**
```c
// Same as Electric_1, except:
txt_page_counter.txt="2/2"
currentPage=1
```

---

## Pages 3-4: Water Section

**Same structure as Electric section:**

1. Copy Electric_1 to Water_1
2. Rename:
   - Page name: Water_1
   - Adjust all page IDs
3. Adjust touch events:
   ```c
   // btn_water Touch Event:
   page Water_2
   ```
4. Page Preinit:
   ```c
   btn_water.pco=2016  // Highlight Water
   currentSection=2
   ```

---

## Pages 5-6: Climate Section

Same process as Water.

---

## Pages 7-8: Status Section

### Special Feature: Status_1 shows all alarms

#### Additional Components for Status_1

**Fuse Display (18 pieces)**

For each fuse:
- **Type**: Text
- **Name**: `fuse_1`, `fuse_2`, ..., `fuse_18`
- **Position**: Grid layout (3 columns x 6 rows)
  - fuse_1: x=170, y=70, w=180, h=40
  - fuse_2: x=370, y=70, w=180, h=40
  - fuse_3: x=570, y=70, w=180, h=40
  - fuse_4: x=170, y=120, w=180, h=40
  - ...etc
- **Font**: Font 1 (16pt)
- **Text**: "Fuse 1 Main"
- **Properties**:
  - `bco`: 65535 (transparent)
  - `pco`: 2016 (green, changes dynamically to red on alarm)
  - `xcen`: 0 (left aligned)

**Tank Level Display**

```
Position below fuses:

Fresh Water:  [Tank-Icon] 45L (60%) 🟢
Gray Water:   [Tank-Icon] 28L (40%) 🟢
Black Water:  [Tank-Icon] 12L (20%) 🔴
LPG:          [Tank-Icon] 3.2kg (40%) 🟢
```

For each tank:
- **Type**: Text + Number
- **Name**: `tank_fresh`, `tank_fresh_pct`
- Dynamic color change via ESPHome

---

## Pages 9-10: Power Section

Same process as other sections.

---

## Best Practices

### 1. Reusable Components

**Workflow:**
1. Create Home page completely
2. Copy scrolling text + 5 buttons
3. Paste on each sub-page
4. Only adjust touch events

### 2. Consistent Naming

```
Pages:      Electric_1, Electric_2, Water_1, ...
Buttons:    btn_electric, btn_water, btn_next
Text:       txt_alert, txt_page_counter
Fuses:      fuse_1, fuse_2, ...
Tanks:      tank_fresh, tank_gray, ...
```

### 3. Transparency for All Components

For **every** button/text:
```
bco=65535   (Background transparent)
pco=65535   (Text white standard)
pco2=65535  (Press color transparent)
```

### 4. Touch Events Structure

```c
// Always:
currentSection=X  // Tracking
currentPage=Y     // Tracking
page PageName     // Navigation
```

### 5. Fonts

- **Font 0**: 12pt - Small, details
- **Font 1**: 16pt - Standard text
- **Font 2**: 20pt - Buttons
- **Font 3**: 24pt - Scrolling text
- **Font 4**: 32pt - Large values

---

## Testing Checklist

- [ ] All buttons lead to correct page
- [ ] Page counter shows correct values
- [ ] Transparency works (no background visible)
- [ ] Scrolling text scrolls for long text
- [ ] Navigation between sections works
- [ ] Navigation within section works
- [ ] Page preinit events set correct button colors
- [ ] UART communication works (test command: `txt_alert.txt="TEST"`)

---

## Tips & Tricks

### Quick Component Copying

1. Right-click on component → Copy
2. Right-click on page → Paste
3. Adjust position/name

### Bulk Editing with Nextion

Unfortunately not possible - adjust each component manually.

**Workaround**: Create first page perfectly, then copy entire page as template.

### HMI File Backup

Nextion Editor only saves .HMI (binary).
**No version control possible!**

→ Create regular backups:
```
Project_v1.0.HMI
Project_v1.1.HMI
Project_WORKING.HMI
```

---

## Troubleshooting

### "Component not found" Error

- **Cause**: Name misspelled or component doesn't exist on current page
- **Solution**: Check exact spelling (case-sensitive!)

### Scrolling doesn't work

- **Cause**: `dis=0` or text too short
- **Solution**: Set `dis=1`, text longer than component width

### Button doesn't work

- **Cause**: Touch event not set or page ID wrong
- **Solution**: Check touch release event, verify target page exists

### Transparency shows black background

- **Cause**: `bco` not set to 65535
- **Solution**: Set all background colors to 65535

---

## Next Steps

1. ✅ Nextion project created
2. → Create ESPHome config (see `/esphome/display-base.yaml`)
3. → Implement Master Warning/Caution logic
4. → Add your own sensors

See [Design Guide](design-guide.md) for more details!

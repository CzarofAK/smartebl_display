# Customization Guide

**Customizing SmartEBL Display for Your Needs**

---

## Table of Contents

1. [Add Your Own Sensors](#add-your-own-sensors)
2. [Create New Sections](#create-new-sections)
3. [Customize Alarm Thresholds](#customize-alarm-thresholds)
4. [Change Layout](#change-layout)
5. [Customize Colors](#customize-colors)
6. [Add More Pages](#add-more-pages)

---

## Add Your Own Sensors

### Step 1: Define Sensor in ESPHome

```yaml
# In your main.yaml or a section file

sensor:
  # Example: Refrigerator temperature
  - platform: homeassistant
    id: fridge_temperature
    entity_id: sensor.fridge_temp
    internal: false
    on_value:
      then:
        - lambda: |-
            // Update Nextion Display
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.1f°C", x);
            id(nextion_display).set_component_text("fridge_temp", buffer);
            
            // Change color if too warm
            int color = 2016;  // Green
            if (x > 8.0) {
              color = 64512;  // Orange (too warm)
            }
            if (x > 10.0) {
              color = 63488;  // Red (critical)
            }
            id(nextion_display).set_component_value("fridge_temp.pco", color);
```

### Step 2: Create Component in Nextion

1. Open Nextion project
2. Go to corresponding page (e.g. Climate_1)
3. Add **Text Component**:
   - Name: `fridge_temp`
   - Position: x=200, y=150
   - Size: w=200, h=40
   - Font: Font 2
   - Text: "0.0°C" (placeholder)
   - `bco`: 65535 (transparent)
   - `pco`: 2016 (green)
4. Save & compile
5. Flash to display

### Step 3: Test

```bash
esphome run motorhome-display.yaml
```

In log you should see:
```
[homeassistant] 'fridge_temperature': Got state 5.2
[display] Setting component 'fridge_temp' text to '5.2°C'
```

---

## Create New Sections

### Example: Add "Lighting" Section

#### 1. Create Nextion Pages

Currently: Pages 0-10 in use

New pages:
- Page 11: Lighting_1
- Page 12: Lighting_2

**In Nextion Editor:**

1. **Right-click on page list → Add Page**
2. Name: `Lighting_1`
3. Page ID: 11
4. **Copy components** from Electric_1:
   - Scrolling text
   - 5 menu buttons
   - Page counter
   - Next button

**Add menu button on Home (Page 0):**

1. Copy `btn_power`
2. Rename to `btn_lighting`
3. Position: x=0, y=510 (below Power)
4. Text: "Lighting"
5. Touch Release Event:
   ```c
   currentSection=6
   currentPage=0
   page Lighting_1
   ```

**Touch Events on Lighting_1:**

```c
// btn_lighting Touch Release Event:
currentPage++
if(currentPage>=lightingPages)
{
  currentPage=0
}
page Lighting_2

// btn_electric Touch Release Event:
currentSection=1
currentPage=0
page Electric_1

// ... etc for other buttons
```

**Page Preinit Event:**
```c
// Highlight Lighting button
btn_lighting.pco=2016
btn_electric.pco=65535
btn_water.pco=65535
btn_climate.pco=65535
btn_status.pco=65535
btn_power.pco=65535

// Page counter
txt_page_counter.txt="1/2"

// Tracking
currentSection=6
currentPage=0
```

#### 2. Create ESPHome Section

Create `esphome/sections/lighting.yaml`:

```yaml
# ============================================
# Lighting Section
# ============================================

sensor:
  - platform: homeassistant
    id: interior_light_brightness
    entity_id: light.interior
    attribute: brightness
    on_value:
      then:
        - lambda: |-
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.0f%%", (x / 255.0) * 100.0);
            id(nextion_display).set_component_text("int_brightness", buffer);

binary_sensor:
  - platform: homeassistant
    id: interior_light_state
    entity_id: light.interior
    on_press:
      then:
        - lambda: |-
            id(nextion_display).set_component_text("int_status", "ON");
            id(nextion_display).set_component_value("int_status.pco", 2016);
    on_release:
      then:
        - lambda: |-
            id(nextion_display).set_component_text("int_status", "OFF");
            id(nextion_display).set_component_value("int_status.pco", 50712);

switch:
  - platform: homeassistant
    id: interior_light
    entity_id: light.interior
```

#### 3. Import in Main Config

In `motorhome-display.yaml`:

```yaml
packages:
  display_base: !include display-base.yaml
  alarms: !include display-alarms.yaml
  electric: !include sections/electric.yaml
  status: !include sections/status.yaml
  lighting: !include sections/lighting.yaml  # ← NEW
```

---

## Customize Alarm Thresholds

### Change Battery Thresholds

In `display-alarms.yaml`:

```yaml
binary_sensor:
  - platform: template
    id: battery_critical
    lambda: |-
      // Standard: < 20%
      return (id(battery_soc).state < 20);
      
      // Customize: e.g. < 15%
      return (id(battery_soc).state < 15);
```

### Add Custom Alarm Logic

```yaml
binary_sensor:
  # Example: Temperature alarm
  - platform: template
    id: temp_too_high
    lambda: |-
      return (id(internal_temperature).state > 35.0);
    on_press:
      - script.execute: check_alarms
    on_release:
      - script.execute: check_alarms
```

In `check_alarms` script:

```yaml
script:
  - id: check_alarms
    then:
      - lambda: |-
          bool warning = false;
          
          // Existing checks...
          if (id(battery_critical).state) {
            warning = true;
          }
          
          // NEW: Temperature check
          if (id(temp_too_high).state) {
            warning = true;
          }
          
          // Rest as before...
```

---

## Change Layout

### Different Display Size (5")

**Nextion:**
- Resolution: 800x480 (same)
- No adjustment needed!

**For 3.5" (480x320):**

```
Menu width:  100px  (instead of 150px)
Fonts:       -2pt   (e.g. Font 1 = 14pt instead of 16pt)
Buttons:     w=100, h=60 (instead of 150x80)
```

### Move Menu (right instead of left)

**Nextion:**

1. Move menu buttons:
   - From: x=0
   - To: x=650 (800 - 150)

2. Adjust content area:
   - From: x=150
   - To: x=0

### Horizontal Menu (top)

```
┌─────────────────────────────────────────────────┐
│ Alert Text                                      │
├──────┬──────┬──────┬──────┬──────┬─────────────┤
│ Elec │Water │Clima │Statu │Power │  [Page]     │
├──────┴──────┴──────┴──────┴──────┴─────────────┤
│                                                 │
│                Content Area                     │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Button size:**
- w=150, h=50
- Position: y=50, x=0/150/300/450/600

---

## Customize Colors

### Custom Color Scheme

**Example: Blue Scheme**

```yaml
# In display-alarms.yaml

script:
  - id: check_alarms
    then:
      - lambda: |-
          if (warning) {
            // Red → Blue
            id(nextion_display).set_component_value("txt_alert.pco", 31);  // Blue
          } else if (caution) {
            // Orange → Turquoise
            id(nextion_display).set_component_value("txt_alert.pco", 2047);  // Turquoise
          } else {
            // Green stays
            id(nextion_display).set_component_value("txt_alert.pco", 2016);
          }
```

**Color Calculator:**
```
RGB → Nextion Decimal
R: 0-31 (5 bit)
G: 0-63 (6 bit)
B: 0-31 (5 bit)

Decimal = (R << 11) | (G << 5) | B

Example Blue (0, 0, 255):
R=0, G=0, B=31
Decimal = 31 = 0x001F
```

**Online tool**: [https://nextion.tech/color-converter/](https://nextion.tech/color-converter/)

---

## Add More Pages

### 3 Pages per Section

Currently: 2 pages (e.g. Electric_1, Electric_2)
Goal: 3 pages (Electric_1, Electric_2, Electric_3)

**Nextion:**

1. **Add Page**: Electric_3 (Page 3)
2. Copy components from Electric_2
3. **Adjust touch events**:

   Electric_1:
   ```c
   // btn_electric → Continue
   page Electric_2
   ```
   
   Electric_2:
   ```c
   // btn_electric → Continue
   page Electric_3
   ```
   
   Electric_3:
   ```c
   // btn_electric → Back to 1
   page Electric_1
   ```

4. **Adjust page counter**:
   - Electric_1: "1/3"
   - Electric_2: "2/3"
   - Electric_3: "3/3"

5. **Change global variable**:
   ```c
   int electricPages=3  // Instead of 2
   ```

---

## Example Templates

### Template: Gauge (Progress Bar)

```yaml
# ESPHome
sensor:
  - platform: homeassistant
    id: water_pressure
    entity_id: sensor.water_pressure
    on_value:
      then:
        - lambda: |-
            // Nextion Progress Bar "j0" (0-100)
            int percent = map(x, 0.0, 5.0, 0, 100);  // 0-5 bar → 0-100%
            id(nextion_display).set_component_value("j0.val", percent);
```

**Nextion:**
- Component: Progress Bar (j0)
- Range: 0-100
- pco: Color (Green 2016)

### Template: Dual-State Button (On/Off)

```yaml
# ESPHome
binary_sensor:
  - platform: homeassistant
    id: pump_state
    entity_id: switch.water_pump
    on_press:
      - lambda: |-
          id(nextion_display).set_component_value("btn_pump.bco", 2016);  // Green BG
          id(nextion_display).set_component_text("btn_pump", "ON");
    on_release:
      - lambda: |-
          id(nextion_display).set_component_value("btn_pump.bco", 63488);  // Red BG
          id(nextion_display).set_component_text("btn_pump", "OFF");
```

**Nextion:**
- Component: Dual-state Button
- Text: "ON" / "OFF"
- bco: Changes between Green/Red

---

## Best Practices

### 1. Naming Convention

```
Sensors:     <section>_<name>        (electric_voltage)
Components:  <short>_<name>          (bat_volt, tank_fresh)
Pages:       <Section>_<number>      (Electric_1, Water_2)
```

### 2. Use Comments

```yaml
# In ESPHome
sensor:
  - platform: homeassistant
    id: battery_voltage
    entity_id: sensor.battery_voltage
    # Updates Nextion "bat_volt" on Electric_1
    on_value:
      # ...
```

### 3. Fallback Values

```yaml
lambda: |-
  if (isnan(x)) {
    id(nextion_display).set_component_text("bat_volt", "---");
  } else {
    char buffer[10];
    snprintf(buffer, sizeof(buffer), "%.1fV", x);
    id(nextion_display).set_component_text("bat_volt", buffer);
  }
```

### 4. Logging

```yaml
lambda: |-
  ESP_LOGI("custom", "Updating fridge temp: %.1f°C", x);
  // ...
```

---

## Troubleshooting Customization

### "Component not found"

**Problem**: ESPHome can't find Nextion component

**Solution:**
- Check exact spelling (case-sensitive!)
- Check if component exists on current page
- Use logs: `ESP_LOGI("test", "Updating component xyz");`

### Color doesn't change

**Problem**: `set_component_value("xxx.pco", color)` doesn't work

**Solution:**
- Check if component supports dynamic color
- Test with known values (2016=Green, 63488=Red)
- Check Nextion component: `pco` must be editable

### Page switching doesn't work

**Problem**: Touch event doesn't lead to correct page

**Solution:**
- Check page name exactly (case-sensitive!)
- Check page ID in Nextion
- Test with `page 1` (ID) instead of `page Electric_1` (name)

---

## Community Examples

After release, community customizations will be linked here:

- [ ] Integration with Victron GX
- [ ] Solar dashboard with graphics
- [ ] Motorhome leveling with MPU6050
- [ ] GPS tracking with Leaflet maps

---

## Further Help

- 📖 [Installation Guide](installation.md)
- 📖 [Nextion Page Structure](../nextion/docs/page-structure.md)
- 📖 [Design Guide](../nextion/docs/design-guide.md)
- 🐛 [GitHub Issues](https://github.com/CzarofAK/smartebl_display/issues)
- 💬 [Discussions](https://github.com/CzarofAK/smartebl_display/discussions)

**Have fun customizing! 🎨**

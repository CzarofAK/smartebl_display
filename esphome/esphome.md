# ESPHome Configuration Guide

Complete guide for ESPHome configuration and customization of SmartEBL Display.

---

## Table of Contents

1. [Package System Architecture](#package-system-architecture)
2. [Integration with SmartEBL](#integration-with-smartebl)
3. [Core Components](#core-components)
4. [Display Sections](#display-sections)
5. [Master Warning/Caution System](#master-warningcaution-system)
6. [Adding Custom Sensors](#adding-custom-sensors)
7. [Customization Examples](#customization-examples)
8. [Best Practices](#best-practices)

---

## Package System Architecture

SmartEBL Display uses ESPHome's **package system** for modular, reusable configuration.

### What are Packages?

Packages allow you to split ESPHome configuration into reusable modules that can be:
- Imported from GitHub repositories
- Included from local files
- Mixed and matched as needed
- Overridden in your main config

### Package Structure

```
SmartEBL Display Packages:
├── display-base.yaml          # Core: ESP32 + Nextion UART
├── display-alarms.yaml         # Master Warning/Caution system
└── sections/                   # Modular display sections
    ├── electric.yaml           # Battery, solar, shore power
    ├── status.yaml             # Fuses, tanks, system status
    ├── water.yaml              # Water systems (template)
    ├── climate.yaml            # Climate control (template)
    └── power.yaml              # Power management (template)
```

### Import Methods

**Method 1: GitHub Import (Recommended)**

```yaml
packages:
  display_base: github://CzarofAK/smartebl_display/esphome/display-base.yaml@main
  electric: github://CzarofAK/smartebl_display/esphome/sections/electric.yaml@main
```

**Advantages:**
- Always up-to-date
- No local files needed
- Easy version control with `@branch` or `@tag`
- Automatic updates

**Method 2: Local File Import**

```yaml
packages:
  display_base: !include display-base.yaml
  electric: !include sections/electric.yaml
```

**Advantages:**
- Works offline
- Full control over changes
- Easier for development/customization

---

## Integration with SmartEBL

SmartEBL Display is designed to work seamlessly with **[SmartEBL](https://github.com/CzarofAK/smartebl)** - a complete motorhome control system.

### Combined Architecture

```
┌─────────────────────────────────────────────┐
│    Your Main Configuration                  │
│    (motorhome-display.yaml)                 │
├─────────────────────────────────────────────┤
│                                             │
│  SmartEBL Packages                          │
│  ├── Sensor configurations                  │
│  ├── Relay controls                         │
│  ├── Home Assistant integration             │
│  └── Automation logic                       │
│                                             │
├─────────────────────────────────────────────┤
│                                             │
│  SmartEBL Display Packages                  │
│  ├── display-base.yaml                      │
│  ├── display-alarms.yaml                    │
│  └── sections/*                             │
│                                             │
└─────────────────────────────────────────────┘
```

### Example Integration

```yaml
# motorhome-display.yaml

substitutions:
  device_name: motorhome-display

packages:
  # Import SmartEBL base system
  # This provides all sensor configurations
  smartebl: github://CzarofAK/smartebl/motorhome.yaml@main

  # Import SmartEBL Display packages
  # This adds the visual interface
  display_base: github://CzarofAK/smartebl_display/esphome/display-base.yaml@main
  display_alarms: github://CzarofAK/smartebl_display/esphome/display-alarms.yaml@main

  # Enable display sections
  electric: github://CzarofAK/smartebl_display/esphome/sections/electric.yaml@main
  status: github://CzarofAK/smartebl_display/esphome/sections/status.yaml@main
```

### Why This Works

1. **SmartEBL** defines all sensor entity_ids from Home Assistant
2. **SmartEBL Display** references those same sensors for display
3. **No duplication** - sensors defined once, used everywhere
4. **Modular** - enable only sections you need

---

## Core Components

### display-base.yaml

**Purpose**: ESP32 + Nextion UART communication

**What it provides:**
- UART configuration (115200 baud)
- Nextion display platform setup
- Component update helpers
- Page navigation functions

**Key Configuration:**

```yaml
# UART for Nextion
uart:
  tx_pin: ${nextion_tx_pin}  # Default: GPIO17
  rx_pin: ${nextion_rx_pin}  # Default: GPIO16
  baud_rate: 115200

# Nextion Display
display:
  - platform: nextion
    id: nextion_display
    uart_id: uart_bus
    update_interval: 5s
```

**Customization via Substitutions:**

```yaml
# In your main config
substitutions:
  nextion_tx_pin: GPIO4   # Change pins
  nextion_rx_pin: GPIO5
```

---

### display-alarms.yaml

**Purpose**: Master Warning/Caution system (aviation-inspired)

**Alarm Levels:**

| Level | Color | Text | Priority | Usage |
|-------|-------|------|----------|-------|
| **Normal** | Green (2016) | ✓ ALL SYSTEMS NORMAL | 0 | All OK |
| **Caution** | Orange (64512) | ⚡ MASTER CAUTION | 1 | Warning, not critical |
| **Warning** | Red (63488) | ⚠️ MASTER WARNING | 2 | Critical, action needed |

**Alarm Logic:**

```yaml
script:
  - id: check_alarms
    mode: restart
    then:
      - lambda: |-
          bool warning = false;
          bool caution = false;

          // Check all critical systems
          if (id(battery_critical).state) {
            warning = true;  # Battery < 20%
          }

          if (id(tank_full).state) {
            caution = true;  # Tank > 80%
          }

          // Update display
          if (warning) {
            id(nextion_display).set_component_text("txt_alert", "⚠️ MASTER WARNING");
            id(nextion_display).set_component_value("txt_alert.pco", 63488);
          } else if (caution) {
            id(nextion_display).set_component_text("txt_alert", "⚡ MASTER CAUTION");
            id(nextion_display).set_component_value("txt_alert.pco", 64512);
          } else {
            id(nextion_display).set_component_text("txt_alert", "✓ ALL SYSTEMS NORMAL");
            id(nextion_display).set_component_value("txt_alert.pco", 2016);
          }
```

**Adding Custom Alarms:**

```yaml
# In your main config
binary_sensor:
  - platform: template
    id: custom_alarm
    lambda: |-
      # Your alarm condition
      return (id(temperature).state > 40.0);
    on_press:
      - script.execute: check_alarms
    on_release:
      - script.execute: check_alarms
```

---

## Display Sections

### electric.yaml - Electric Section

**What it provides:**
- Battery voltage, current, SOC
- Solar power monitoring
- Shore power status
- Alternator status

**Required Nextion Components:**

```
Electric_1 Page:
- bat_volt      (Text) → Battery Voltage
- bat_current   (Text) → Battery Current
- bat_soc       (Text) → State of Charge
- solar_power   (Text) → Solar Power
- shore_status  (Text) → Shore Connected/Disconnected
- alt_power     (Text) → Alternator Power
- alt_status    (Text) → Alternator Status
```

**Example Sensor:**

```yaml
sensor:
  - platform: homeassistant
    id: batterie_spannung
    entity_id: sensor.battery_voltage  # ← CUSTOMIZE!
    on_value:
      then:
        - lambda: |-
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.1fV", x);
            id(nextion_display).set_component_text("bat_volt", buffer);
```

**Color-Coded SOC:**

```yaml
# Battery SOC with color coding
sensor:
  - platform: homeassistant
    id: batterie_soc
    entity_id: sensor.battery_soc
    on_value:
      then:
        - lambda: |-
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.0f%%", x);
            id(nextion_display).set_component_text("bat_soc", buffer);

            // Change color based on SOC
            int color = 2016;  // Green
            if (x < 20) {
              color = 63488;  // Red (critical)
            } else if (x < 40) {
              color = 64512;  // Orange (low)
            }
            id(nextion_display).set_component_value("bat_soc.pco", color);
```

---

### status.yaml - Status Section

**What it provides:**
- 18 fuses monitoring
- Tank levels (fresh, gray, black, LPG)
- System status overview

**Required Nextion Components:**

```
Status_1 Page:
- sich_1 to sich_18 (Text) → Fuse indicators
- tank_fresh       (Text) → Fresh water level
- tank_gray        (Text) → Gray water level
- tank_black       (Text) → Black water level
- tank_lpg         (Text) → LPG level
```

**Fuse Monitoring:**

```yaml
binary_sensor:
  - platform: homeassistant
    id: status_sich_1
    entity_id: binary_sensor.fuse_1_main  # ← CUSTOMIZE
    name: "Fuse 1 Main"
    on_state:
      then:
        - lambda: |-
            // Red if blown, Green if OK
            int color = x ? 63488 : 2016;
            id(nextion_display).set_component_value("sich_1.pco", color);
```

**Tank Level with Percentage:**

```yaml
sensor:
  - platform: homeassistant
    id: tank_fresh_water
    entity_id: sensor.fresh_water_tank  # ← CUSTOMIZE
    on_value:
      then:
        - lambda: |-
            float tank_max = 100.0;  // ← CUSTOMIZE tank size
            float percent = (x / tank_max) * 100.0;

            // Color based on level
            int color = 2016;  // Green
            if (percent < 20) {
              color = 63488;  // Red (low)
            } else if (percent < 40) {
              color = 64512;  // Orange (getting low)
            }

            // Update display
            char buffer[20];
            snprintf(buffer, sizeof(buffer), "%.0fL (%.0f%%)", x, percent);
            id(nextion_display).set_component_text("tank_fresh", buffer);
            id(nextion_display).set_component_value("tank_fresh.pco", color);
```

---

### water.yaml - Water Section (Template)

**What it provides:**
- Fresh/Gray/Black water tanks
- Water pump status
- Water pressure

**Customization Required:**
- Replace entity_ids with your sensors
- Adjust tank sizes
- Enable/disable components as needed

---

### climate.yaml - Climate Section (Template)

**What it provides:**
- Interior/Exterior temperature
- Humidity monitoring
- Heater status
- Ventilation control

**Example Temperature Sensor:**

```yaml
sensor:
  - platform: homeassistant
    id: temperature_inside
    entity_id: sensor.interior_temperature  # ← CUSTOMIZE
    on_value:
      then:
        - lambda: |-
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.1f°C", x);
            id(nextion_display).set_component_text("temp_inside", buffer);
```

---

### power.yaml - Power Section (Template)

**What it provides:**
- Energy consumption
- Power sources overview
- Inverter status

---

## Adding Custom Sensors

### Basic Pattern

All sensors follow this pattern:

```yaml
sensor:
  - platform: homeassistant
    id: unique_sensor_id
    entity_id: sensor.your_entity_id
    on_value:
      then:
        - lambda: |-
            // Format value
            char buffer[20];
            snprintf(buffer, sizeof(buffer), "%.1f", x);

            // Update Nextion component
            id(nextion_display).set_component_text("component_name", buffer);

            // Optional: Change color based on value
            int color = (x > threshold) ? 63488 : 2016;
            id(nextion_display).set_component_value("component_name.pco", color);
```

### Step-by-Step: Add Refrigerator Temperature

**1. Define Sensor in ESPHome**

```yaml
# In your main config or custom section
sensor:
  - platform: homeassistant
    id: fridge_temperature
    entity_id: sensor.fridge_temp
    internal: false
    on_value:
      then:
        - lambda: |-
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.1f°C", x);
            id(nextion_display).set_component_text("fridge_temp", buffer);

            // Color: Red if too warm
            int color = 2016;  // Green
            if (x > 8.0) {
              color = 64512;  // Orange (too warm)
            }
            if (x > 10.0) {
              color = 63488;  // Red (critical)
            }
            id(nextion_display).set_component_value("fridge_temp.pco", color);
```

**2. Create Nextion Component**

- Open Nextion project
- Go to Climate_1 (or appropriate page)
- Add Text component:
  - Name: `fridge_temp`
  - Position: x=200, y=150
  - Size: w=200, h=40
  - Font: Font 2 (20pt)
  - Text: "0.0°C" (placeholder)
  - bco: 65535 (transparent)
  - pco: 2016 (green)

**3. Test**

```bash
esphome logs motorhome-display.yaml
```

Expected output:
```
[homeassistant] 'fridge_temperature': Got state 5.2
[display] Setting component 'fridge_temp' text to '5.2°C'
```

---

## Customization Examples

### Example 1: Add Water Pressure Gauge

**ESPHome:**

```yaml
sensor:
  - platform: homeassistant
    id: water_pressure
    entity_id: sensor.water_pressure
    on_value:
      then:
        - lambda: |-
            // Map 0-5 bar to 0-100%
            int percent = map(x, 0.0, 5.0, 0, 100);
            id(nextion_display).set_component_value("j0.val", percent);

            // Update text
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.1f bar", x);
            id(nextion_display).set_component_text("pressure_text", buffer);
```

**Nextion:**
- Add Progress Bar (j0): Range 0-100
- Add Text (pressure_text): Display value

---

### Example 2: Dual-State Pump Button

**ESPHome:**

```yaml
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

switch:
  - platform: homeassistant
    id: water_pump
    entity_id: switch.water_pump
```

**Nextion:**
- Add Dual-state Button (btn_pump)
- Touch Press Event: Send command to ESP32

---

### Example 3: Create New "Lighting" Section

**Step 1: Create Section File**

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
```

**Step 2: Import in Main Config**

```yaml
packages:
  # ... existing packages ...
  lighting: !include sections/lighting.yaml  # ← NEW
```

**Step 3: Create Nextion Pages**

- Add pages: Lighting_1, Lighting_2
- Add menu button on all pages
- Configure navigation
- See [nextion.md](../nextion/nextion.md) for details

---

## Best Practices

### 1. Naming Conventions

```
Sensors:     <section>_<name>        (e.g., electric_voltage)
Components:  <short>_<name>          (e.g., bat_volt, tank_fresh)
IDs:         snake_case              (e.g., battery_soc)
Pages:       <Section>_<number>      (e.g., Electric_1, Water_2)
```

### 2. Use Comments

```yaml
sensor:
  - platform: homeassistant
    id: battery_voltage
    entity_id: sensor.battery_voltage
    # Updates Nextion "bat_volt" on Electric_1
    on_value:
      # Format: "12.8V"
      then:
        - lambda: |-
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%.1fV", x);
            id(nextion_display).set_component_text("bat_volt", buffer);
```

### 3. Handle Missing Values

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

### 4. Use Logging for Debugging

```yaml
lambda: |-
  ESP_LOGI("custom", "Updating fridge temp: %.1f°C", x);
  char buffer[10];
  snprintf(buffer, sizeof(buffer), "%.1f°C", x);
  id(nextion_display).set_component_text("fridge_temp", buffer);
```

### 5. Override Package Values

```yaml
# Override sensor from package
sensor:
  - id: !extend battery_voltage
    entity_id: sensor.my_custom_battery  # ← Override
```

---

## Troubleshooting

### Component Not Found

**Problem**: `Component 'bat_volt' not found`

**Solution:**
- Check exact spelling (case-sensitive!)
- Verify component exists on current page
- Use logs to debug: `ESP_LOGI("test", "Updating xyz");`

### Entity Unavailable

**Problem**: Sensor shows `unknown` in Home Assistant

**Solution:**
- Verify entity_id in Home Assistant (Developer Tools → States)
- Check entity is available
- Ensure Home Assistant connection is active

### Color Doesn't Change

**Problem**: `set_component_value("xxx.pco", color)` doesn't work

**Solution:**
- Test with known values (2016=Green, 63488=Red)
- Check Nextion component supports dynamic color
- Verify `pco` property is set correctly

---

## Next Steps

- **[Installation Guide](../docs/installation.md)** - Complete installation
- **[Nextion Documentation](../nextion/nextion.md)** - Create Nextion pages
- **[Design Guide](../docs/design.md)** - Design principles

---

## Further Reading

- **[ESPHome Official Docs](https://esphome.io/)**
- **[Nextion Display Platform](https://esphome.io/components/display/nextion.html)**
- **[Home Assistant Integration](https://esphome.io/components/api.html)**
- **[Lambda Expressions](https://esphome.io/guides/automations.html#lambda)**

**Happy customizing! 🎨**

# Installation Guide

Complete step-by-step installation guide for SmartEBL Display.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Hardware Wiring](#hardware-wiring)
3. [Nextion Display Setup](#nextion-display-setup)
4. [ESPHome Configuration](#esphome-configuration)
5. [Flashing & Testing](#flashing--testing)
6. [Troubleshooting](#troubleshooting)
7. [OTA Updates](#ota-updates)

---

## Prerequisites

### Hardware Requirements

- **ESP32 Development Board** (ESP32-WROOM-32, ESP32-DevKitC, or similar)
- **Nextion Display 7"** Enhanced (NX8048P070 recommended)
  - Alternative: NX8048T070 Basic (with reduced features)
- **Power Supply** 5V/1A minimum (2A recommended for stable operation)
- **Jumper Cables** 4 wires for UART connection
- **MicroSD Card** (optional, for Nextion flashing via SD)
- **USB Cable** (data-capable, not charge-only!)

### Software Requirements

- **Nextion Editor** - [Download](https://nextion.tech/nextion-editor/)
- **ESPHome** - Latest version
  - Web Tool: [https://web.esphome.io/](https://web.esphome.io/)
  - Or CLI: `pip install esphome`
- **Home Assistant** (recommended for sensor integration)
- **Git** (optional, for cloning repository)

---

## Hardware Wiring

### ESP32 to Nextion Connection

```
ESP32 Pin          Nextion Pin         Wire Color (typical)
──────────         ───────────         ────────────────────
GPIO17 (TX2)   →   RX                  Yellow
GPIO16 (RX2)   ←   TX                  Blue
GND            →   GND                 Black
5V             →   +5V                 Red
```

### Important Notes

⚠️ **CRITICAL:**
- **Use 5V**, NOT 3.3V for Nextion power! (Display will not work on 3.3V)
- **Cross TX/RX**: ESP32 TX → Nextion RX, ESP32 RX ← Nextion TX
- **Use UART2** on ESP32 (pins can be customized via substitutions)
- **Data cable**: Ensure USB cable supports data transfer (not charge-only)

### Wiring Diagram

```
┌────────────────┐           ┌─────────────────┐
│     ESP32      │           │    Nextion      │
│                │           │                 │
│ GPIO17 (TX2)───┼──────────→│ RX (Yellow)     │
│                │           │                 │
│ GPIO16 (RX2)───┼←──────────┤ TX (Blue)       │
│                │           │                 │
│ GND ───────────┼───────────┤ GND (Black)     │
│                │           │                 │
│ 5V ────────────┼───────────┤ +5V (Red)       │
│                │           │                 │
└────────────────┘           └─────────────────┘
        │
        │ USB (for programming)
        ↓
    Computer
```

### Power Considerations

- **ESP32**: Can be powered via USB or external 5V
- **Nextion**: Requires stable 5V/1A minimum
  - Insufficient power = flickering display
  - Recommendation: Use 5V/2A power supply for both

---

## Nextion Display Setup

### Step 1: Download Nextion Editor

1. Go to [https://nextion.tech/nextion-editor/](https://nextion.tech/nextion-editor/)
2. Download for your OS (Windows/Mac)
3. Install and launch

### Step 2: Create or Load HMI Project

**Option A: Create from Scratch**
- See [nextion.md](../nextion/nextion.md) for complete page structure guide
- See [design.md](design.md) for design principles

**Option B: Use Existing HMI** (if provided)
- Open `.HMI` file in Nextion Editor
- Customize as needed

### Step 3: Compile HMI to TFT

1. In Nextion Editor: **File → Compile**
2. Select **NX8048P070** (or your model)
3. Output: `project_name.tft` file

### Step 4: Flash to Nextion Display

**Method 1: MicroSD Card (Recommended)**

1. Copy `.tft` file to microSD card (FAT32 formatted)
2. **Rename to short name** (e.g., `display.tft`)
3. Insert SD card into Nextion display
4. Power on Nextion
5. Display auto-updates (shows progress bar)
6. Remove SD card when complete

**Method 2: USB-Serial Adapter**

1. Connect USB-Serial to Nextion (RX/TX/GND)
2. In Nextion Editor: **File → Upload**
3. Select COM port, baud rate 115200
4. Click Upload
5. Wait for completion

---

## ESPHome Configuration

### Step 1: Project Structure

This project uses **GitHub package imports** to integrate with SmartEBL:

```
your-esphome-project/
├── motorhome-display.yaml    # Your main config
└── secrets.yaml               # WiFi & API credentials
```

### Step 2: Create secrets.yaml

Create `secrets.yaml` with your credentials:

```yaml
# WiFi Configuration
wifi_ssid: "Your_WiFi_Network"
wifi_password: "YourWiFiPassword"

# API Encryption (generate with: esphome config)
api_encryption_key: "32-character-base64-key-here"

# OTA Password
ota_password: "YourOTAPassword"

# Fallback AP Password (for ESP32 fallback hotspot)
ap_password: "FallbackPassword123"
```

### Step 3: Create Main Configuration

Create `motorhome-display.yaml`:

```yaml
# ============================================
# SmartEBL Display - Main Configuration
# ============================================

substitutions:
  device_name: motorhome-display
  friendly_name: "Motorhome Display"

  # UART Pins for Nextion (customize if needed)
  nextion_tx_pin: GPIO17
  nextion_rx_pin: GPIO16

# ============================================
# ESP32 Board Configuration
# ============================================

esphome:
  name: ${device_name}
  friendly_name: ${friendly_name}

esp32:
  board: esp32dev
  framework:
    type: arduino

# ============================================
# Network Configuration
# ============================================

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot
  ap:
    ssid: "${device_name} Fallback"
    password: !secret ap_password

# Captive portal for easy WiFi setup
captive_portal:

# Home Assistant API
api:
  encryption:
    key: !secret api_encryption_key

# OTA Updates
ota:
  - platform: esphome
    password: !secret ota_password

# Web Server (optional - useful for debugging)
web_server:
  port: 80

# ============================================
# Logging (CRITICAL: baud_rate MUST be 0!)
# ============================================

logger:
  level: INFO
  baud_rate: 0  # ← CRITICAL: Frees UART for Nextion!

# ============================================
# Package Imports (Modular System)
# ============================================

packages:
  # OPTION 1: Import from SmartEBL repository (recommended)
  # This imports all SmartEBL sensor configurations
  smartebl: github://CzarofAK/smartebl/motorhome.yaml@main

  # SmartEBL Display - Core Components
  display_base: github://CzarofAK/smartebl_display/esphome/display-base.yaml@main
  display_alarms: github://CzarofAK/smartebl_display/esphome/display-alarms.yaml@main

  # SmartEBL Display - Sections (enable what you need)
  electric: github://CzarofAK/smartebl_display/esphome/sections/electric.yaml@main
  status: github://CzarofAK/smartebl_display/esphome/sections/status.yaml@main

  # Optional sections (uncomment to enable)
  # water: github://CzarofAK/smartebl_display/esphome/sections/water.yaml@main
  # climate: github://CzarofAK/smartebl_display/esphome/sections/climate.yaml@main
  # power: github://CzarofAK/smartebl_display/esphome/sections/power.yaml@main

# ============================================
# OPTION 2: Local File Imports (for development)
# ============================================
# Uncomment if you cloned the repository locally:
#
# packages:
#   display_base: !include esphome/display-base.yaml
#   alarms: !include esphome/display-alarms.yaml
#   electric: !include esphome/sections/electric.yaml
#   status: !include esphome/sections/status.yaml

# ============================================
# Custom Sensors & Overrides
# ============================================

# Add your own custom sensors here
# Example:
# sensor:
#   - platform: homeassistant
#     id: custom_temperature
#     entity_id: sensor.my_temperature
```

### Configuration Explained

**Key Sections:**

1. **substitutions**: Variables used throughout config
2. **wifi/api/ota**: Network connectivity
3. **logger**: `baud_rate: 0` is CRITICAL (frees UART for Nextion)
4. **packages**: Modular imports from GitHub
   - `smartebl`: Main sensor configuration from SmartEBL repo
   - `display_base`: Nextion UART and core display
   - `display_alarms`: Master Warning/Caution system
   - `sections/*`: Individual display sections

### Step 4: Validate Configuration

Before flashing, validate your config:

```bash
esphome config motorhome-display.yaml
```

This checks for syntax errors and shows compiled configuration.

---

## Flashing & Testing

### Initial Flash (USB Required)

**Using ESPHome Web (Easiest):**

1. Go to [https://web.esphome.io/](https://web.esphome.io/)
2. Click **"Connect"**
3. Select your ESP32 COM port
4. Click **"Install"**
5. Choose **"Install from File"**
6. Upload your `motorhome-display.yaml`
7. Wait for compilation and flash (2-5 minutes)

**Using ESPHome CLI:**

```bash
# Install ESPHome (if not already installed)
pip install esphome

# Compile and flash
esphome run motorhome-display.yaml

# Select: "Plug into this computer"
# Select your COM port
```

### Monitoring Logs

After flashing, monitor logs to verify communication:

```bash
esphome logs motorhome-display.yaml
```

**Expected Log Output:**

```
[INFO] [display] Nextion is connected
[INFO] [homeassistant] Connected to Home Assistant
[INFO] [sensor.battery_voltage] Got value: 12.8
[INFO] [display] Setting component 'bat_volt' to '12.8V'
```

### Testing Checklist

- [ ] ESP32 boots successfully
- [ ] WiFi connects to network
- [ ] Home Assistant API connects
- [ ] Nextion display shows pages
- [ ] Touch navigation works
- [ ] Sensor values update on display
- [ ] Master Warning text appears
- [ ] Colors change dynamically

---

## Troubleshooting

### Display Issues

#### Display Shows Nothing

**Symptoms**: Black screen, no backlight

**Solutions:**
1. **Check power supply**
   - Verify 5V/1A minimum
   - Try different power source
   - Check voltage with multimeter
2. **Verify Nextion flashed**
   - Reflash `.tft` file via microSD
   - Check for flash success message
3. **Test with Nextion Debug Tool**
   - Download from Nextion website
   - Connect via USB-Serial
   - Send test command: `page 0`

#### Display Flickers or Dims

**Cause**: Insufficient power

**Solutions:**
- Use 5V/2A power supply (not 1A)
- Check USB cable quality
- Use shorter/thicker power cables
- Power Nextion and ESP32 separately

#### Touch Not Working

**Solutions:**
- Recalibrate touch in Nextion Editor
- Verify touch events are configured
- Check component `sendxy` property
- Test with Nextion Simulator first

---

### ESP32 Issues

#### ESP32 Won't Boot / Constant Restart

**Cause**: Usually UART conflict

**Solutions:**
1. **Verify logger baud_rate**
   ```yaml
   logger:
     baud_rate: 0  # MUST be 0!
   ```
2. **Try different UART pins**
   ```yaml
   substitutions:
     nextion_tx_pin: GPIO4
     nextion_rx_pin: GPIO5
   ```
3. **Check USB cable**
   - Use data cable (not charge-only)
   - Try different cable/port

#### "Component Not Found" Error

**Symptoms**: ESPHome log shows `Component 'bat_volt' not found`

**Solutions:**
- **Check spelling** (case-sensitive!)
  ```cpp
  // ESPHome
  set_component_text("bat_volt", "12.8V")

  // Nextion component name must match exactly
  // Component: bat_volt
  ```
- **Verify component exists on current page**
  - Component must be on visible page
  - Check page switching logic
- **Reflash Nextion completely**
  - Delete old `.tft` from SD card
  - Flash new version

---

### ESPHome Connection Issues

#### No WiFi Connection

**Symptoms**: ESP32 can't connect to WiFi

**Solutions:**
1. **Verify secrets.yaml**
   ```yaml
   wifi_ssid: "Correct_Network_Name"
   wifi_password: "CorrectPassword"
   ```
2. **Check WiFi band** (ESP32 supports 2.4GHz only, NOT 5GHz)
3. **Check signal strength**
   - Move ESP32 closer to router
   - Use external antenna
4. **Enable fallback AP**
   ```yaml
   wifi:
     # ... existing config ...
     ap:
       ssid: "${device_name} Fallback"
       password: !secret ap_password
   ```
   - Connect to fallback network
   - Configure WiFi via captive portal

#### No Home Assistant Connection

**Symptoms**: ESPHome logs show `API connection failed`

**Solutions:**
1. **Verify API encryption key**
   - Generate new key: `esphome config motorhome-display.yaml`
   - Copy key to `secrets.yaml`
   - Add to Home Assistant integration
2. **Check Home Assistant logs**
   - Settings → System → Logs
   - Look for ESPHome errors
3. **Restart ESP32**
   ```bash
   esphome run motorhome-display.yaml
   ```
4. **Re-add integration in Home Assistant**
   - Settings → Devices & Services
   - Add Integration → ESPHome
   - Enter ESP32 IP address

#### Sensors Show No Values

**Cause**: Entity IDs don't match Home Assistant

**Solutions:**
1. **Verify entity_id in Home Assistant**
   - Developer Tools → States
   - Find exact entity ID
2. **Update section files**
   ```yaml
   sensor:
     - platform: homeassistant
       entity_id: sensor.victron_battery_voltage  # ← Use exact ID
   ```
3. **Check entity availability**
   - Ensure entity exists and has value
   - Check Home Assistant logs

---

### UART Communication Issues

#### No Communication Between ESP32 and Nextion

**Symptoms**: Display doesn't update, no response from Nextion

**Solutions:**
1. **Verify wiring**
   - TX → RX (cross-connected)
   - RX → TX
   - Common ground (GND)
2. **Check baud rate**
   ```yaml
   # In display-base.yaml (should be 115200)
   uart:
     baud_rate: 115200
   ```
3. **Test with simple command**
   ```yaml
   # Add to your config
   on_boot:
     then:
       - lambda: |-
           id(nextion_display).send_command_printf("page 0");
   ```
4. **Enable debug logging**
   ```yaml
   logger:
     level: DEBUG
     baud_rate: 0
   ```

---

### Common Configuration Mistakes

#### ❌ Wrong Baud Rate

```yaml
# WRONG - conflicts with Nextion UART
logger:
  baud_rate: 115200
```

```yaml
# CORRECT - frees UART for Nextion
logger:
  baud_rate: 0
```

---

#### ❌ Placeholder Entity IDs Not Changed

```yaml
# WRONG - template placeholder
sensor:
  - platform: homeassistant
    entity_id: sensor.battery_voltage  # Template!
```

```yaml
# CORRECT - your actual entity
sensor:
  - platform: homeassistant
    entity_id: sensor.victron_battery_voltage
```

---

#### ❌ Wrong GPIO Pins

```yaml
# WRONG - using GPIO1/GPIO3 (Serial0, used by USB)
substitutions:
  nextion_tx_pin: GPIO1
  nextion_rx_pin: GPIO3
```

```yaml
# CORRECT - using GPIO16/GPIO17 (Serial2)
substitutions:
  nextion_tx_pin: GPIO17
  nextion_rx_pin: GPIO16
```

---

## OTA Updates

After initial USB flash, you can update wirelessly (OTA = Over-The-Air).

### Via ESPHome Web

1. Go to [https://web.esphome.io/](https://web.esphome.io/)
2. Click **"Connect"**
3. Select **"Wirelessly"**
4. Enter ESP32 IP address (find in Home Assistant or router)
5. Upload updated `motorhome-display.yaml`

### Via ESPHome CLI

```bash
esphome run motorhome-display.yaml

# Select: "Wirelessly"
# Enter IP address or select from discovered devices
```

### Via Home Assistant

1. Settings → Devices & Services → ESPHome
2. Find your device
3. Click **"Update"**
4. Select new firmware

### OTA Troubleshooting

**OTA Fails:**
- Check ESP32 is on same network
- Verify OTA password in secrets.yaml
- Try USB flash if OTA repeatedly fails
- Check Home Assistant logs

---

## Next Steps

✅ **Installation Complete!**

Now you can:

1. **Customize Sections** - See [esphome.md](../esphome/esphome.md)
2. **Adjust Design** - See [design.md](design.md)
3. **Modify Nextion Pages** - See [nextion.md](../nextion/nextion.md)
4. **Add Custom Sensors** - See package documentation

---

## Getting Help

If you encounter issues not covered here:

- 📖 **[ESPHome Documentation](../esphome/esphome.md)**
- 📖 **[Nextion Documentation](../nextion/nextion.md)**
- 🐛 **[GitHub Issues](https://github.com/CzarofAK/smartebl_display/issues)**
- 💬 **[Discussions](https://github.com/CzarofAK/smartebl_display/discussions)**
- 📚 **[ESPHome Community](https://community.home-assistant.io/c/esphome/)**

**Good luck with your installation! 🚀**

Here is a complete, formatted Markdown template that you can copy and paste directly into your GitHub repository's `README.md` file. It covers the true hardware specs, the mislabeled memory quirks, the exact pinouts, and the base ESPHome code needed to run the lab-grade incubator simulator box smoothly.

---

# ESP32-S3 DevKitC-1 (N8R2) - Incubator Simulator Controller

This repository contains the hardware documentation and base ESPHome configuration for the ESP32-S3 microcontroller used as the main brain for a Lab-Grade incubator simulator box.

This specific build drives a high-resolution 240x320 ST7789V TFT display, utilizing external PSRAM for smooth, anti-aliased TrueType fonts and flicker-free UI rendering.

## ⚠️ Important Hardware Notice: Mislabeled Memory

This board was originally sold/labeled as an **N16R8** (16MB Flash, 8MB Octal PSRAM). However, physical hardware probing via boot logs revealed the factory actually populated it as an **N8R2**.

Configuring the software for 16MB/Octal on this 8MB/Quad board will result in instant fatal exceptions, continuous boot loops, and Wi-Fi crashes. **You must use the exact memory configuration provided below for the board to remain stable.**

### True Hardware Specifications

* **Core:** ESP32-S3 (Dual Core, 160MHz)
* **Flash Memory:** 8MB (Must be declared in ESPHome to prevent boot crashes)
* **External RAM (PSRAM):** 2MB
* **PSRAM Protocol:** Quad SPI @ 40MHz (Not Octal)
* **USB Ports:** 2 (1x Native USB, 1x UART/COM for serial flashing/logging)

## 🔌 Wiring & Pinout Guide

The board communicates with the ST7789V display over a high-speed SPI bus.

| Display Pin | ESP32-S3 GPIO | Function |
| --- | --- | --- |
| **CLK / SCL** | GPIO12 | SPI Clock |
| **MOSI / SDA** | GPIO11 | SPI Data Input |
| **CS** | GPIO8 | Chip Select |
| **DC** | GPIO9 | Data / Command |
| **RST** | GPIO10 | Reset |
| **BLK** | GPIO7 | Backlight (Configured as PWM/Switch) |
| **VCC** | 3.3V | Power |
| **GND** | GND | Ground |

## 🛠️ Critical ESPHome Fixes & Quirks

### 1. Memory Configuration

To unlock the external RAM for the display buffer without crashing the board, the memory must be strictly defined:

```yaml
esp32:
  board: esp32-s3-devkitc-1
  flash_size: 8MB
psram:
  mode: quad
  speed: 40MHz

```

### 2. The Display "Color Fix"

By default, the ST7789V driver may render a negative image and swap Red for Blue. You must apply these color overrides in the `display:` block:

```yaml
invert_colors: false
color_order: BGR

```

### 3. Wi-Fi Stability under Load

When driving high-resolution graphics from PSRAM, the ESP32's power management can drop the Wi-Fi connection. To prevent disconnects, disable Wi-Fi power saving:

```yaml
wifi:
  power_save_mode: none

```

## 💻 Base ESPHome Configuration

Here is the stable, baseline YAML template to get the board online and the display rendering correctly. *Note: You must place your chosen `.ttf` font file (e.g., `Roboto_Condensed-Bold.ttf`) in the same directory as this YAML file.*

```yaml
esphome:
  name: esp32-s3-incubator
  friendly_name: Simulator Controller

esp32:
  board: esp32-s3-devkitc-1
  framework:
    type: arduino
  flash_size: 8MB

psram:
  mode: quad
  speed: 40MHz

logger:
  baud_rate: 115200

# Required to use the "Free RAM" sensors
debug:

sensor:
  - platform: debug
    free:
      name: "Free Internal RAM"
    psram:
      name: "Free PSRAM"

api:
ota:

wifi:
  ssid: "YOUR_SSID"
  password: "YOUR_PASSWORD"
  power_save_mode: none
  ap:
    ssid: "ESP32 Hotspot"

# --- FONT DEFINITION ---
font:
  - file: "Roboto_Condensed-Bold.ttf"
    id: font_main
    size: 20

# --- SPI BUS ---
spi:
  clk_pin: GPIO12
  mosi_pin: GPIO11

# --- BACKLIGHT CONTROL ---
output:
  - platform: gpio
    pin: GPIO7
    id: backlight_out
light:
  - platform: binary
    output: backlight_out
    name: "Display Backlight"
    restore_mode: ALWAYS_ON

# --- DISPLAY CONFIGURATION ---
display:
  - platform: ili9xxx
    model: ST7789V
    id: tft
    cs_pin: GPIO8
    dc_pin: GPIO9
    reset_pin: GPIO10
    
    # Color Fixes
    invert_colors: false
    color_order: BGR
    rotation: 90°
    
    dimensions:
      width: 240
      height: 320

    lambda: |-
      it.fill(Color(12, 12, 20)); // Dark Background
      it.print(it.get_width() / 2, 20, id(font_main), Color::WHITE, TextAlign::CENTER, "SYSTEM ONLINE");

```

---

This should give anyone looking at your GitHub repository (including yourself a few months from now!) a perfect understanding of exactly what this board is and how to program it.

Would you like me to help you map out the wiring and YAML code for the actual temperature and humidity sensors next?


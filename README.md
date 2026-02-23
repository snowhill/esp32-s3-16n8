Here is a refined, processor-focused Markdown template for your GitHub repository. I have completely stripped out all the display, font, and UI code, leaving only the pure technical documentation for the ESP32-S3 chip, its memory quirks, and the baseline configuration needed to keep it stable.

---

# ESP32-S3 DevKitC-1 - Hardware & Memory Documentation

This repository documents the core microcontroller configuration for the incubator simulator project. The main processor is an **ESP32-S3**, but specific memory configurations are required to prevent fatal crashes and boot loops.

## ⚠️ Important Hardware Notice: Mislabeled Memory (N16R8 vs N8R2)

It is highly common for ESP32-S3 boards sourced online to be mislabeled by the seller. This specific board was sold as an **N16R8** model (16MB Flash, 8MB Octal PSRAM), but physical hardware probing revealed it is actually an **N8R2** model.

If you attempt to flash this board using `flash_size: 16MB` or `mode: octal`, the board will experience instant fatal exceptions (`rtc_sw_cpu_rst`), constant boot loops, and Wi-Fi initialization failures.

**You must use the exact 8MB / Quad memory configuration provided below for the board to remain stable.**

### True Hardware Specifications

* **Core:** ESP32-S3 (Dual Core, 160MHz)
* **Physical Flash Memory:** 8MB
* **External RAM (PSRAM):** 2MB
* **PSRAM Protocol:** Quad SPI @ 40MHz (Not Octal)
* **USB Ports:** 2 (1x Native USB, 1x UART/COM for serial flashing and log reading)

## 🔍 How to Verify True Hardware Memory

If you are setting up a new board and need to verify its actual memory chips, do not rely on the seller's description. Plug the board into your computer via the UART/COM port and view the serial boot log.

Look for these exact lines during the initial boot sequence:

* **To verify Flash:** `I (30) spi_flash: detected size(8192k)` *(Confirms 8MB Flash)*
* **To verify PSRAM:** `I (45) esp_psram: Found 2MB PSRAM device` *(Confirms 2MB RAM)*

## 💻 Baseline ESPHome Configuration

Here is the stable, bare-minimum YAML template required to get this specific ESP32-S3 online, allocate its memory correctly, and expose diagnostic sensors to Home Assistant so you can monitor RAM usage in real-time.

```yaml
esphome:
  name: esp32-s3-core
  friendly_name: Simulator Controller

# 1. MATCH THE PHYSICAL FLASH SIZE
esp32:
  board: esp32-s3-devkitc-1
  framework:
    type: arduino
  flash_size: 8MB

# 2. ACTIVATE THE EXTERNAL RAM (Standard Quad Mode)
psram:
  mode: quad
  speed: 40MHz

# Enable serial logging (115200 baud recommended for S3 boards)
logger:
  baud_rate: 115200

# 3. ENABLE LIVE MEMORY DIAGNOSTICS
debug:

sensor:
  - platform: debug
    # Monitors standard internal memory
    free:
      name: "Free Internal RAM"
    # Monitors the 2MB external chip we enabled above
    psram:
      name: "Free PSRAM"

api:
ota:

# 4. PREVENT WI-FI CRASHES UNDER LOAD
wifi:
  ssid: "YOUR_SSID"
  password: "YOUR_PASSWORD"
  # Disabling power save mode prevents the Wi-Fi modem from sleeping 
  # and dropping off the network when the processor is under heavy load.
  power_save_mode: none

  ap:
    ssid: "ESP32 Fallback Hotspot"

```

---

This gives you a clean, processor-only foundation for your GitHub repository.

Would you like me to help you draft the next section of your documentation covering the specific ESP32-S3 GPIO pins you plan to use for your incubator's sensors (like your temperature/humidity modules) and relays?

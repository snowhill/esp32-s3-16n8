# ESP32-S3 Blink

Minimal Arduino blink sketch for an ESP32-S3 board.

## Build with arduino-cli

Install the ESP32 Arduino core:

```sh
arduino-cli core update-index
arduino-cli core install esp32:esp32
```

Compile the sketch:

```sh
arduino-cli compile --fqbn esp32:esp32:esp32s3 .
```

Upload to your board, replacing `/dev/ttyACM0` with the correct port:

```sh
arduino-cli upload -p /dev/ttyACM0 --fqbn esp32:esp32:esp32s3 .
```

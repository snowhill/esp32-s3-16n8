# Compiled ESP32-S3 Blink Results

Compiled with:

```sh
arduino-cli compile --fqbn esp32:esp32:esp32s3 /tmp/blink-work/blink/ --output-dir /tmp/blink-work/compiled/
```

## Binary file sizes

| File | Size |
| --- | ---: |
| `blink.ino.bin` | 300800 bytes |
| `blink.ino.bootloader.bin` | 19984 bytes |
| `blink.ino.merged.bin` | 4194304 bytes |
| `blink.ino.partitions.bin` | 3072 bytes |

## Upload commands

Merged binary:

```sh
esptool.py --chip esp32s3 --baud 921600 write_flash 0x0 blink.ino.merged.bin
```

Separate binaries:

```sh
esptool.py --chip esp32s3 --baud 921600 --before default_reset --after hard_reset write_flash -z 0x0 blink.ino.bootloader.bin 0x8000 blink.ino.partitions.bin 0x10000 blink.ino.bin
```

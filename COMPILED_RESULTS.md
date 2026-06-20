# ESP32-S3 Blink Compile Results

Compiled in the cloud environment with:

```sh
arduino-cli compile --fqbn esp32:esp32:esp32s3 blink/ --output-dir /tmp/compiled/
```

Toolchain versions:

- `arduino-cli` 1.5.1
- `esp32:esp32` Arduino core 3.3.10

## Compiled Output Files

Output from `ls -la /tmp/compiled/`:

```text
total 18220
drwxr-xr-x 2 ubuntu ubuntu    4096 Jun 20 05:15 .
drwxrwxrwt 1 root   root      4096 Jun 20 05:15 ..
-rw-r--r-- 1 ubuntu ubuntu  300800 Jun 20 05:15 blink.ino.bin
-rw-r--r-- 1 ubuntu ubuntu   19984 Jun 20 05:15 blink.ino.bootloader.bin
-rwxr-xr-x 1 ubuntu ubuntu 6709080 Jun 20 05:15 blink.ino.elf
-rw-r--r-- 1 ubuntu ubuntu 7414453 Jun 20 05:15 blink.ino.map
-rw-r--r-- 1 ubuntu ubuntu 4194304 Jun 20 05:15 blink.ino.merged.bin
-rw-r--r-- 1 ubuntu ubuntu    3072 Jun 20 05:15 blink.ino.partitions.bin
```

| File | Size (bytes) |
| --- | ---: |
| `/tmp/compiled/blink.ino.bin` | 300800 |
| `/tmp/compiled/blink.ino.bootloader.bin` | 19984 |
| `/tmp/compiled/blink.ino.elf` | 6709080 |
| `/tmp/compiled/blink.ino.map` | 7414453 |
| `/tmp/compiled/blink.ino.merged.bin` | 4194304 |
| `/tmp/compiled/blink.ino.partitions.bin` | 3072 |

## Memory Usage

Compiler output:

```text
Sketch uses 300652 bytes (22%) of program storage space. Maximum is 1310720 bytes.
Global variables use 21992 bytes (6%) of dynamic memory, leaving 305688 bytes for local variables. Maximum is 327680 bytes.
```

- Program storage usage: 300652 bytes (22%) of 1310720 bytes.
- Dynamic memory usage: 21992 bytes (6%) of 327680 bytes, leaving 305688 bytes for local variables.

## Upload Command

`arduino-cli board list` output in the cloud environment:

```text
No boards found.
```

No ESP32-S3 serial port is attached in the cloud environment, so replace `<PORT>` with the board's serial device, such as `/dev/ttyACM0` or `/dev/ttyUSB0`:

```sh
arduino-cli upload -p <PORT> --fqbn esp32:esp32:esp32s3 --input-dir /tmp/compiled/ blink/
```

# Firmware overview

Status: Phase 1 design only; no firmware is implemented in this repository.

`CONFIRMED`: The firmware target is PlatformIO with ESP-IDF, C/C++, FreeRTOS, the `esp32-s3-devkitc-1` board ID, native USB, and UART fallback.

The device plans to:

- generate mock temperature, pH, light, nitrate, phosphate, and potassium samples every second;
- refresh the OLED every second;
- timestamp data using NTP, PCF8563 holdover, or explicit unsynchronized quality;
- append to MicroSD and build roughly ten-sample batches every ten seconds;
- publish over MQTT/TLS at QoS 1 and wait for application acknowledgements;
- keep operating locally through network loss;
- cache the active user-defined algae profile for local threshold alerts;
- accept configuration/commands and later perform signed HTTPS OTA with rollback.

BLE provisioning through Flutter is the planned user path. SoftAP is a development fallback. See [components](firmware-components.md), [tasks](freertos-task-design.md), and [offline storage](offline-storage-design.md).

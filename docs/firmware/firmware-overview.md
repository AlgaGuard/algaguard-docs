# Firmware overview

Status: Phase 1 design baseline. The separate firmware repository now contains a buildable and host-tested credential foundation; physical ESP32 execution remains unverified.

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

**CONFIRMED:** The credential foundation defines local non-exportable key handles, CSR submission, bounded public certificate/chain storage, exact `deviceId`/SAN `deviceUuid` validation, configurable MQTT TLS endpoint loading, a two-certificate rotation state machine, recovery, revoked/expired handling, log redaction, and synchronized/unsynchronized-time decisions. It does not embed a development CA private key.

**TBD:** Encrypted NVS/flash integration, secure-boot/flash-encryption policy, real TLS/CSR execution on the ESP32, and real-board storage/memory behavior still require physical validation. No secure element is claimed.

BLE provisioning through Flutter is the planned user path. SoftAP is a development fallback. See [components](firmware-components.md), [tasks](freertos-task-design.md), [offline storage](offline-storage-design.md), the [credential lifecycle](../backend/device-credential-lifecycle.md), and [transport limits](../architecture/transport-security-limits.md).

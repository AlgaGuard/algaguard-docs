# ESP32-S3 board profile

| Field | Value |
|---|---|
| Board family | `CONFIRMED` Espressif ESP32-S3-DevKitC-1 |
| Revision | `CONFIRMED` v1.1 from supplied pinout and GPIO38 RGB LED |
| PlatformIO board ID | `CONFIRMED` `esp32-s3-devkitc-1` |
| Retailer configuration | `VENDOR-LISTED` N16R8, 16 MB flash, 8 MB PSRAM |
| Physical module marking | `TBD` pending clear shield photograph or transcription |
| Runtime memory report | `TBD` until firmware bring-up |
| Onboard RGB | GPIO38; reserved |
| Native USB | `CONFIRMED` required; GPIO19/20 reserved |
| UART0 fallback | `CONFIRMED` required; GPIO43/44 reserved |

## Configuration conflict

The retailer describes N16R8 and also uses wording that may not align with Espressif's official N16R8V ordering information. Do not resolve this by inference. Capture the exact metal-shield text, compare it with Espressif ordering data, and verify flash and PSRAM at runtime in Phase 3.

For octal flash/PSRAM boards, Espressif states GPIO35, GPIO36, and GPIO37 are used internally. They are not allocated. The supplied [v1.1 pinout](../../assets/diagrams/esp32-s3-devkitc-1-pinout.png) and the [preliminary pin plan](preliminary-pin-plan.md) are the review inputs.

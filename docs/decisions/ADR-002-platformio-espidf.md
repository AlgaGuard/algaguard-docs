# ADR-002: PlatformIO and ESP-IDF firmware

## Status

Accepted

## Date

2026-07-22

## Context

The ESP32-S3 device needs FreeRTOS, secure networking, offline storage, BLE provisioning, and OTA foundations.

## Decision

Use PlatformIO with ESP-IDF and C/C++ for `algaguard-firmware`.

## Alternatives

Arduino framework alone; raw ESP-IDF project without PlatformIO; another MCU ecosystem.

## Consequences

The team must manage ESP-IDF configuration and hardware-specific testing.

## Benefits

Direct access to Espressif security, networking, FreeRTOS, and OTA capabilities with repeatable builds.

## Risks

Toolchain complexity and board-configuration mistakes.

## Follow-up

Verify the exact module, flash, PSRAM, USB, and UART behavior in Phase 3.

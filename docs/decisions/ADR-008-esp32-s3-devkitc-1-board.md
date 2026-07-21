# ADR-008: ESP32-S3-DevKitC-1 board

## Status

Accepted with physical verification pending

## Date

2026-07-22

## Context

The prototype board and supplied v1.1 pinout are selected, but retailer/module wording conflicts remain.

## Decision

Target ESP32-S3-DevKitC-1 v1.1 with PlatformIO ID `esp32-s3-devkitc-1`. Treat N16R8, 16 MB flash, and 8 MB PSRAM as confirmed selections/vendor claims pending shield and runtime verification. Reserve native USB, UART0, onboard RGB, strapping, and octal-memory pins.

## Alternatives

Another ESP32-S3 board; accept retailer wording without verification.

## Consequences

Phase 3 must verify marking/memory before final configuration.

## Benefits

Strong connectivity, memory, native USB, and documented ESP-IDF support.

## Risks

Wrong variant or revision could invalidate pins/memory assumptions.

## Follow-up

Photograph the shield and record runtime flash/PSRAM results.

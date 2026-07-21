# ADR-009: PCF8563 RTC and NTP hierarchy

## Status

Accepted

## Date

2026-07-22

## Context

The device needs traceable timestamps across internet and power interruptions.

## Decision

Use NTP UTC as authoritative, PCF8563 as holdover, and boot-relative time when neither is valid. Store timestamp quality as `NTP_SYNCED`, `RTC_HOLDOVER`, or `UNSYNCED`; health includes `lastTimeSyncAt` and `rtcValid`. OLED and RTC share I2C GPIO8/GPIO9.

## Alternatives

NTP only; RTC as permanent authority; fabricate time from last known value.

## Consequences

Contracts and UI must represent missing UTC and integrity state.

## Benefits

Honest time provenance and offline continuity.

## Risks

RTC drift, invalid backup cell, or mishandled voltage-low flag.

## Follow-up

Measure module drift and define Phase 2 timestamp schemas.

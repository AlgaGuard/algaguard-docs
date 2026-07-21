# ADR-007: Offline-first device behavior

## Status

Accepted

## Date

2026-07-22

## Context

Network, cloud, RTC, or SD availability can vary near the tank; local monitoring must degrade explicitly.

## Decision

Local display and sampling continue without network. Store at least two days on MicroSD, replay acknowledged batches after reconnect, cache the active profile, use RTC holdover, and mark unsynchronized timestamps honestly. SD failure must not stop the UI.

## Alternatives

Cloud-dependent operation; RAM-only queue; stop sampling without trusted UTC.

## Consequences

Firmware needs durable queue recovery, time-quality state, data-loss reporting, and cached configuration.

## Benefits

Useful local behavior and traceable recovery through outages.

## Risks

Storage corruption, replay storms, stale profiles, or ambiguous time.

## Follow-up

Define storage encoding and offline reconciliation contracts in Phases 2 and 4.

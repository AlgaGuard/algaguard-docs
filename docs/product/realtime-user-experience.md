# Realtime user experience

Status: **CONFIRMED Phase 2.1 product behavior; client implementation is planned for later application phases.**

React and Flutter load authoritative page state through HTTPS, then use WSS to keep visible data current. Realtime delivery improves responsiveness but never replaces the state-recovery path.

## Expected client behavior

1. Authenticate with Keycloak and load the current authorized view over HTTPS.
2. Request a short-lived, one-time connection ticket from the API Gateway.
3. Connect to the Realtime Service using WSS.
4. Subscribe only to the organization, device, and current-user events needed for the visible experience.
5. Apply recognized events by stable identifier and ignore duplicate `eventId` values within a bounded cache.
6. Show a clear stale/reconnecting state when the connection is unavailable; do not imply live status.
7. After a disconnect, back off with jitter, request a new ticket, reconnect, fetch the latest authoritative state through HTTPS, resubscribe, and resume live updates.

## Product flows

- Dashboards receive telemetry, health, device status, alerts, and profile configuration changes.
- A command begins as an authorized HTTPS request. The UI may then show `command.status.changed` progress and results received over WebSocket.
- OTA views receive rollout status without allowing OTA actions directly over WebSocket.
- Current-user notifications are explicitly subscribed and authorized; a successful connection is not blanket access.

WebSocket updates may be missed, delayed, duplicated, or reordered. Clients therefore reconcile events with authoritative versions, resource sequences, or refreshed HTTPS state. Exact heartbeat, timeout, message-size, subscription, and rate limits are **TBD** and must produce accessible user feedback when enforced.

See the [Realtime Service](../backend/realtime-service.md) and [ADR-017](../decisions/ADR-017-websocket-realtime-service.md).

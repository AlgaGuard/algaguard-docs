# Wi-Fi provisioning design

The planned user path is BLE provisioning through the Flutter application. SoftAP is a development fallback, not the primary production onboarding flow.

## BLE flow

1. The device enters a time-limited provisioning state and advertises a non-secret identifier.
2. The mobile app scans a device QR/claim value and connects over BLE.
3. User presence and device identity are verified by a Phase 2 contract; exact proof is `TBD`.
4. The app sends Wi-Fi credentials through an encrypted/authenticated provisioning session.
5. The device stores credentials using ESP-IDF secure storage capabilities appropriate to the threat model.
6. The device connects, obtains trusted time, reaches the API/MQTT endpoints, and returns a result.
7. Provisioning stops and sensitive buffers are cleared.

Never log Wi-Fi passwords, claim tokens, device secrets, or access tokens. Rate-limit attempts and define secure reset/re-provision behavior. The Wi-Fi reset button combination is `TBD`.

See [device onboarding](../product/device-onboarding.md) for account and claiming behavior.

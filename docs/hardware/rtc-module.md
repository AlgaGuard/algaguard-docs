# PCF8563 RTC module

The PCF8563 is the planned offline holdover clock. NTP remains authoritative when internet access is available.

## Electrical and interface plan

- Share I2C GPIO8 SDA and GPIO9 SCL with the OLED using 3.3 V logic.
- Discover actual device addresses during the Phase 3 I2C scan.
- RTC INT on GPIO17 is optional and should remain disconnected unless a requirement appears. CLKOUT is not required for basic timestamping.
- `VENDOR-LISTED`: The module accepts a CR1220 3 V backup coin cell, which is not included.
- `SAFETY`: Confirm module charging circuitry and coin-cell compatibility before fitting a battery. The backup cell must power only the RTC holdover circuit, not the ESP32 or other modules.

## Integrity and drift

The PCF8563 provides a voltage-low integrity indication. Firmware must reject RTC time when that condition shows the clock may be unreliable.

NXP states that frequency tolerance depends on the crystal, capacitance, and device variation, and describes adjusted designs achieving about plus or minus five minutes per year. The complete retailer module's actual crystal and layout are not specified.

- `ASSUMPTION`: Use a 20 ppm planning envelope, about 1.7 seconds per day, until measurements replace it.
- `TBD`: Characterize drift across expected temperature and power conditions.
- Synchronize from NTP after connectivity returns, write the corrected UTC value to the RTC, and initially retry at least daily while online. The final interval is configurable and based on drift tests.

See the authoritative [timekeeping design](../firmware/timekeeping-design.md) and the [NXP PCF8563 data sheet](https://cache.nxp.com/docs/en/data-sheet/PCF8563.pdf).

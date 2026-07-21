# Hardware inventory and product references

Links and page claims were checked on 2026-07-22. A reachable retailer page confirms only what that seller lists; it is not a manufacturer guarantee. Procurement must re-check stock, revision, and specifications.

| Item | Status and recorded claim | Reference and check result |
|---|---|---|
| ESP32-S3-DevKitC-1 | `CONFIRMED` family and v1.1 pinout; `VENDOR-LISTED` N16R8, 16 MB flash, 8 MB PSRAM | [Retailer](https://tronic.lk/product/esp32-s3-devkitc-1-dev-board-n16r8-wifi-bluetooth-iot) reachable; [Espressif v1.1 guide](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32s3/esp32-s3-devkitc-1/user_guide_v1.1.html) reachable |
| OLED | `VENDOR-LISTED` 1.3 inch, 128x64, white, I2C, SSD1306, 3.3 V to 5 V | [Retailer](https://tronic.lk/product/1-3-inch-128x64-oled-display-module-white-i2c-iic) reachable |
| MicroSD module | `VENDOR-LISTED` SPI, 4.5 V to 5.5 V supply, onboard 3.3 V regulator and level conversion | [Retailer](https://tronic.lk/product/sd-card-module-for-arduino) reachable |
| MicroSD card | `CONFIRMED` 32 GB card already available | Physical make, endurance, format, and health are `TBD` |
| RTC module | `VENDOR-LISTED` PCF8563 I2C module; CR1220 3 V cell not included | [Retailer](https://tronic.lk/product/pcf8563-rtc-real-time-clock-module-i2c) and [NXP PCF8563 data sheet](https://cache.nxp.com/docs/en/data-sheet/PCF8563.pdf) reachable |
| Cells | `CONFIRMED` three cells in 3S; `VENDOR-LISTED` Samsung INR18650-25P or exact supplied product, nominal 3.6 V, 2500 mAh, unprotected | [Retailer](https://alphatronic.lk/product/18650-li-ion-rechargeable-battery-inr18650/) reachable, but detailed cell values were not exposed in accessible page text; verify label/datasheet |
| BMS candidate | `VENDOR-LISTED` 3S, headline 20 A continuous discharge upper limit and 10 A charge upper limit | [Retailer](https://alphatronic.lk/product/3s-18650-20a-battery-management-system-bms/) reachable; ratings require review |
| Charger candidate | `VENDOR-LISTED` Type-C input, 3S, 12.6 V charging output, page lists 0.74 A charging current for its 2 A input version | [Retailer](https://alphatronic.lk/product/type-c-bms-3s-2a-lithium-battery-charge-module/) reachable |
| External adapter candidate | `VENDOR-LISTED` 12 V, 2 A, center-positive, 5.5 mm x 2.1 mm barrel connector | [Retailer](https://www.duino.lk/product/power-supply-adapter-3-pin-ac-dc-12v-2a/) reachable |
| Red LED | `VENDOR-LISTED` 3 mm diffused low-current LED | [Retailer](https://tronic.lk/product/led-3mm-red-diffused-pack-approx-10pcs) reachable |
| Green LED | `VENDOR-LISTED` 3 mm diffused low-current LED | [Retailer](https://tronic.lk/product/led-3mm-green-diffused-pack-approx-10pcs) reachable |
| Blue LED | `VENDOR-LISTED` 3 mm diffused low-current LED | [Retailer](https://tronic.lk/product/led-3mm-blue-diffused-pack-approx-10pcs) reachable |
| Preferred enclosure | `CONFIRMED` preferred prototype candidate is 200 x 120 x 55 mm; `VENDOR-LISTED` product EN0006 says no clamp and water-resistant wording; fit not proven | [Retailer](https://tronic.lk/product/white-box-enclosure-waterproof-200-x-120-x-55mm-no-clam) was confirmed through its indexed product result on 2026-07-22; the direct page was intermittently unavailable |
| Compact enclosure | `VENDOR-LISTED` 135 x 70 x 50 mm and water-resistant wording | [Retailer](https://tronic.lk/product/white-box-enclosure-waterproof-135-x-70-x-50mm-24) reachable; do not select without fit study |
| M12 three-way connector candidate | `VENDOR-LISTED` IP65 and 2 A | [Retailer](https://tronic.lk/product/m12-3-way-waterproof-connector-for-led-strip) reachable; not suitable for claimed continuous submersion |

`SAFETY`: Retailer product pages do not approve a combined battery, charger, BMS, load-sharing, fusing, wiring, or enclosure design. See [power architecture](power-architecture.md).

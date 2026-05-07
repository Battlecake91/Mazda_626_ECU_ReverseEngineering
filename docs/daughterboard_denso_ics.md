# Daughterboard Denso ICs

## Overview

The secondary / interface board carries several Denso custom ICs that interface the main ECU logic to external automotive signals and power-stage circuitry.

Known board / subassembly number: `079721-3521`.

## IC001 SE134

| Pin | Connection |
|---:|---|
| 4 | Connected from external `D7`, shared reset net from main board (`IC4:34`, `IC7:8`). |
| 6 | GND. |
| 7 | +5 V. |
| 10 | +12 V. |
| 11 | +12 V. |

Current role: unknown Denso custom interface IC, possibly reset / supervision / signal-conditioning related.

## IC250 MP611

| Pin | Connection |
|---:|---|
| 3 | GND. |
| 12 | +5 V. |
| 15 | GND. |
| 19 | Connected to shared `IC700:1+2` and `IC701:1+2` line. |

Current role: unknown Denso custom IC. The connection to the shared SE074 pins suggests diagnostic, enable, mode, or current / fault processing.

## IC700 SE074

See [SE074 injector driver hypothesis](se074_injector_driver_hypothesis.md).

Known supplies:

| Pin | Net |
|---:|---|
| 3 | GND |
| 8 | GND |
| 9 | +12 V |
| 14 | GND |
| 16 | +5 V |

## IC701 SE074

See [SE074 injector driver hypothesis](se074_injector_driver_hypothesis.md).

Known supplies:

| Pin | Net |
|---:|---|
| 3 | +5 V |
| 8 | GND |
| 9 | +12 V |
| 14 | +5 V |
| 16 | +5 V |

## IC500 D151821-0020

`IC500` is treated as the daughterboard equivalent of main-board `IC6`.

It feeds multiple inputs to `IC3`, the memory-mapped 74HC541 input port.

| IC500 pin | Route to IC3 |
|---:|---|
| 3 | `IC3 A0` through connector `A5`. |
| 4 | `IC3 A3` through connector `B6`. |
| 6 | `IC3 A5` through connector `B7`. |
| 7 | `IC3 A1` through connector `A4`. |
| 8 | `IC3 A2` through connector `B5`. |
| 10 | `IC3 A6` through connector `A6`. |
| 11 | `IC3 A7` through connector `B9`. |

See [IC6 / IC500 level shifter](ic6_ic500_level_shifter.md) for behavior.

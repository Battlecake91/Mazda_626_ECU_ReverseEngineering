# IC1 SC402617FN Pin Mapping

## Overview

`IC1` is treated as the main MCU or a Denso / Motorola-HC11-related custom MCU. Its external bus behavior, address decoding, and firmware layout are compatible enough with an HC11-style workflow to make that the current analysis model.

## Known Address Pins

| Address bit | IC1 pin | Notes |
|---:|---:|---|
| `A0` | 78 | Also used as register select bit for peripherals. |
| `A1` | 77 | Also used as register select bit for peripherals. |
| `A2` | 76 | Used by `IC7` timer RS2. |
| `A3` | 75 | External address bus. |
| `A4` | 74 | External address bus. |
| `A5` | 73 | External address bus. |
| `A6` | 72 | External address bus. |
| `A7` | 71 | External address bus. |
| `A8` | 21 | External address bus. |
| `A9` | 20 | External address bus. |
| `A10` | 19 | External address bus. |
| `A11` | 18 | External address bus. |
| `A12` | 17 | Shared by RAM and ROM. |
| `A13` | 16 | ROM address / decode related. |
| `A14` | 15 | ROM address / decode related. |

## Data Bus

| Data bit | IC1 pin | Connected devices |
|---:|---:|---|
| `D0 / IO0` | 49 | ROM + RAM data bus. |
| `D1 / IO1` | 50 | ROM + RAM data bus. |
| `D2 / IO2` | 51 | ROM + RAM data bus. |
| `D3 / IO3` | 52 | ROM + RAM data bus. |
| `D4 / IO4` | 53 | ROM + RAM data bus. |
| `D5 / IO5` | 54 | ROM + RAM data bus. |
| `D6 / IO6` | 55 | ROM + RAM data bus. |
| `D7 / IO7` | 56 | ROM + RAM data bus. |

`IC3` also drives this bus through its `74HC541` outputs when selected and during read cycles.

## Bus Control / Glue Logic Pins

| IC1 pin | Connected net / devices | Current interpretation |
|---:|---|---|
| 47 | Through `F3` to `IC20:1`, `IC10:27`, `IC4:21`, `IC7:13` | `R/W` net. Used by `IC20` to generate global read `/OE`. |
| 48 | Through `F4` to `IC20:2`, `IC10:26`, `IC7:17`, `IC4:25`, `IC9:11` | Bus enable / `E` / chip enable timing net. Feeds clock divider and RAM CE2. |

## Power / Ground Pins

| IC1 pin | Net |
|---:|---|
| 22 | VCC |
| 23 | GND |
| 40 | GND |
| 41 | VCC |
| 46 | GND |
| 66 | VCC |
| 83 | VCC |

## Crystal Pins

| IC1 pin | Net |
|---:|---|
| 44 | Crystal / oscillator (`X1`, 8 MHz). |
| 45 | Crystal / oscillator (`X1`, 8 MHz). |

## Known Direct Connections to Denso Driver ICs

| IC1 pin | Destination | Notes |
|---:|---|---|
| 3 | External `D6` -> `IC700:6`, with `R10` pull-up | SE074 control line. |
| 4 | External `C6` -> `IC700:5`, with `R9` pull-up | SE074 control line. |
| 5 | External `C5` -> `IC700:4`, with `R8` pull-up | SE074 control line. |
| 6 | External `C17` -> `IC701:7`, with `R7` pull-up | SE074 enable / bank / mode candidate. |
| 25 | `IC800:4` | SE123 logic-side connection. |
| 26 | External `C8` -> `IC700:7`, with `R18` pull-up | SE074 enable / bank / mode candidate. |
| 27 | `IC800:5` | SE123 logic-side connection. |
| 28 | `IC800:6` | SE123 logic-side connection. |
| 57 | `IC800:8` | SE123 logic-side connection. |
| 58 | `IC800:9` | SE123 logic-side connection. |
| 59 | `IC800:10` | SE123 logic-side connection. |
| 60 | `IC5:4` | SE123 logic-side connection. |
| 80 | `IC5:5` | SE123 logic-side connection. |

## Notes

`IC1` has enough confirmed external bus behavior to justify continuing with an HC11-like model in Ghidra. The exact part identity remains open, so memory layout should be validated from code behavior and bus measurements rather than blind trust in a generic datasheet. Because apparently one custom ECU MCU was not enough suffering.

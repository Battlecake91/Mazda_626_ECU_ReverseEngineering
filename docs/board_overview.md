# Board Overview

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Identification
| Item | Marking / Part Number | Notes |
|---|---|---|
| ECU / Control Unit | `U2103136866B` | Control unit identification found on the ECU. |
| ECU family / readable marking | `KL05` | Only `KL05` is still readable on the ECU label. Remaining serial / part number is scratched and unreadable. |
| PCB / sub-board / interface board | `079721-3521` | Likely Denso / PCB / sub-board number for switching stages, voltage dividers, interface or secondary board circuitry. |
| Vehicle / engine context | Mazda 626 KLDE | KL-series V6 ECU, likely European KL05 variant. |

---


## System Summary

The ECU appears to be a KL-series V6 control unit with a main custom MCU (`SC402617FN`), external SRAM, EPROM, parallel I/O, timer/counter peripheral, glue logic and a secondary/interface board containing Denso custom ICs.

The current documentation treats the readable ECU family as `KL05`. The remaining ECU label data is damaged, so all variant-specific conclusions should remain cautious until compared against known KL05/KLDE ECU variants.

## Main Functional Blocks

| Block | Main Parts | Current Interpretation |
|---|---|---|
| CPU / control core | IC1 `SC402617FN`, X1 8 MHz | Main controller with external memory/peripheral bus. |
| Program memory | IC11 `27C256` | 32 KiB EPROM, probably mapped at `0x8000-0xFFFF`. |
| RAM | IC10 `TC5564APL` | External SRAM, decoded into `0x2C00-0x2FFF` window. |
| Peripheral decode | IC17 `74HC138`, IC20 `74HC00` | Address decode and read/ROM-enable glue logic. |
| Input status port | IC3 `74HC541`, IC6/IC500 `D151821-0020` | Memory-mapped input buffer from level shifter/comparator paths. |
| Parallel I/O | IC4 `HD63BP21P` | PIA at `0x2400-0x27FF`; corrected register order. |
| Timer outputs | IC7 `HD63B40P`, IC9 `74HC74` | Timer selected at `0x2000-0x23FF`, clocked by divided bus-enable clock. |
| Output drivers | IC5/IC800 `SE123`, IC700/IC701 `SE074` | Low-side / injector driver logic, still partly inferred. |

## Clock / Oscillator
| RefDes | Value / Marking | Type | Notes |
|---|---|---|---|
| X1 | 8 MHz | Crystal | Main clock crystal. |
| C1 | 40 pF | 0805 C0G | Likely crystal load capacitor. |
| C2 | 40 pF | 0805 C0G | Likely crystal load capacitor. |

---

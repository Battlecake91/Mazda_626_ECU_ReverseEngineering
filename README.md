# KLDE ECU Reverse Engineering Notes

This repository documents the ongoing reverse engineering work on a Mazda KLDE / KL05 related Denso ECU assembly.

The notes are intentionally written as working documentation: confirmed measurements, plausible interpretations, known conflicts, and open questions are kept separate where possible. This is not a polished schematic replacement yet. It is the pile of silicon archaeology before it grows a tie and pretends to be a specification.

## Known Identifiers

| Item | Marking / identifier | Notes |
|---|---:|---|
| Control unit | `U2103136866B` | ECU / control unit identifier. |
| ECU readable marking | `KL05` | Remaining ECU serial / part number is scratched and unreadable. |
| Secondary / interface PCB | `079721-3521` | Associated with power stages, dividers, interface circuitry, or daughterboard functions. |
| Main project | `KLDE_ECU_Reverse` | Working project name. |

## Documentation Index

### System Overview

- [Board overview](docs/board_overview.md)
- [IC inventory](docs/ic_inventory.md)
- [Component / BOM notes](docs/component_bom_notes.md)
- [Connector and external signal notes](docs/connectors_and_signals.md)
- [Open questions](docs/open_questions.md)

### Main ECU Logic

- [IC1 MCU pin mapping](docs/ic1_pin_mapping.md)
- [External bus and memory map](docs/external_bus_memory.md)
- [Chip select and glue logic](docs/chip_select_glue_logic.md)
- [IC3 74HC541 input port](docs/ic3_input_port.md)
- [IC4 HD63BP21P PIA](docs/ic4_pia.md)
- [IC7 HD63B40P programmable timer](docs/ic7_timer.md)
- [IC9 74HC74 timer clock divider](docs/ic9_timer_clock_divider.md)
- [Peripheral summary](docs/peripherals.md)

### Denso Custom / Interface ICs

- [IC6 and IC500 D151821-0020 level shifter / comparator](docs/ic6_ic500_level_shifter.md)
- [SE123 driver devices](docs/se123_driver_devices.md)
- [SE074 injector driver hypothesis](docs/se074_injector_driver_hypothesis.md)
- [Daughterboard Denso ICs](docs/daughterboard_denso_ics.md)

### Firmware Analysis

- [Ghidra HC11 setup](docs/ghidra_hc11_setup.md)
- [Firmware analysis notes](docs/firmware_analysis.md)

## Confidence Convention

| Label | Meaning |
|---|---|
| Confirmed | Direct continuity measurement, datasheet pinout match, or repeated observation. |
| Strong hypothesis | Fits multiple measurements and known ECU architecture, but still lacks a final schematic proof. |
| Working hypothesis | Plausible and useful for analysis, but should be verified before relying on it. |
| Conflict / unresolved | Known disagreement between measurements, assumptions, or source material. |

## Current High-Level Architecture

The ECU appears to use an external 8-bit data bus around a custom or derivative MCU (`IC1 = SC402617FN`) with external ROM, external RAM, a PIA, a programmable timer, a latched / buffered input port, and several Denso custom interface / driver ICs.

The working external address map currently is:

| Range | Function |
|---:|---|
| `0x0000-0x007F` | Internal MCU registers, according to datasheet-derived reference. |
| `0x0080-0x047F` | Internal MCU RAM, according to datasheet-derived reference. |
| `0x0D80-0x0FFF` | Internal EEPROM, according to datasheet-derived reference. |
| `0x1000-0x10FF` | HC11-compatible internal register area / mirrored register model used during analysis. |
| `0x2000-0x23FF` | `IC7` HD63B40P timer. |
| `0x2400-0x27FF` | `IC4` HD63BP21P PIA. |
| `0x2800-0x2BFF` | `IC3` 74HC541 input port. |
| `0x2C00-0x2FFF` | External RAM (`IC10` TC5564APL). |
| `0x3000-0x3FFF` | Decoder range currently considered unused / unassigned. |
| `0x8000-0xFFBF` | External ROM program area. |
| `0xFFC0-0xFFFF` | Vector area in ROM. |

`0xBE40-0xBFFF` is noted from MCU reference material as boot ROM / vector related space. Its practical relevance for this ECU dump still has to be handled carefully when setting up disassembly and memory blocks.

# KLDE ECU Reverse Engineering

This repository collects the current working notes for reverse engineering a Mazda 626 KLDE / KL05 Denso ECU assembly.

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Current Documentation

| File | Content |
|---|---|
| [`docs/BOM.md`](docs/BOM.md) | Full current BOM / working hardware map, kept as the main source snapshot. |
| [`docs/board_overview.md`](docs/board_overview.md) | Identification, ECU context, board-level overview. |
| [`docs/ic_inventory.md`](docs/ic_inventory.md) | IC list and inferred functions. |
| [`docs/external_bus_memory.md`](docs/external_bus_memory.md) | Address/data bus, external memory map, Ghidra block recommendations. |
| [`docs/chip_select_glue_logic.md`](docs/chip_select_glue_logic.md) | IC17 decoder, IC20 NAND logic, F1-F4 links. |
| [`docs/ic1_pin_mapping.md`](docs/ic1_pin_mapping.md) | Known IC1 / MCU pin connections. |
| [`docs/ic3_input_port.md`](docs/ic3_input_port.md) | 74HC541 memory-mapped input buffer and IC500/IC6 bit mapping. |
| [`docs/ic4_pia.md`](docs/ic4_pia.md) | HD63BP21P PIA wiring, corrected register select mapping, 0x2401 Bit7 interpretation. |
| [`docs/ic7_timer.md`](docs/ic7_timer.md) | HD63B40P timer wiring, outputs, gates, reset and clocking. |
| [`docs/ic9_clock_divider.md`](docs/ic9_clock_divider.md) | 74HC74 two-stage clock divider feeding IC7. |
| [`docs/ic6_ic500_level_shifter.md`](docs/ic6_ic500_level_shifter.md) | D151821-0020 / 151821-0020 comparator / level-shifter behavior. |
| [`docs/se123_driver_devices.md`](docs/se123_driver_devices.md) | SE123 output-driver hypothesis and pin mapping. |
| [`docs/se074_injector_pre_driver.md`](docs/se074_injector_pre_driver.md) | SE074 / injector secondary-board hypothesis. |
| [`docs/connectors_and_outputs.md`](docs/connectors_and_outputs.md) | ECU connector working list and low-side output classes. |
| [`docs/passives.md`](docs/passives.md) | Resistors, capacitors, SOT-23 devices and population notes. |
| [`docs/secondary_board.md`](docs/secondary_board.md) | Secondary-board Denso IC supply pins and known interconnects. |
| [`docs/ghidra_hc11_setup.md`](docs/ghidra_hc11_setup.md) | Ghidra setup notes for HC11-style firmware analysis. |
| [`docs/firmware_analysis.md`](docs/firmware_analysis.md) | Current firmware labels, variables and analysis notes. |
| [`docs/open_questions.md`](docs/open_questions.md) | Unresolved topics and next useful checks. |

## Current High-Level Model

- IC1 is the main custom MCU / CPU with an external 8-bit data bus and external address bus.
- IC11 is a 32 KiB `27C256` EPROM, currently treated as `0x8000-0xFFFF` until the reset vector confirms it.
- IC10 is external SRAM, visible through the decoded external window at `0x2C00-0x2FFF`.
- IC17 `74HC138` decodes external peripheral windows from `0x2000` through `0x2FFF`.
- IC20 `74HC00` generates global read `/OE`, ROM `/CE`, and several status/inverter signals.
- IC4 `HD63BP21P` PIA has corrected register select wiring: Pin35 RS1 = A0 and Pin36 RS0 = A1.
- IC7 `HD63B40P` timer is selected by IC17 `/Y0`; its timer outputs O1/O2/O3 go to the SE074 injector pre-driver path.
- IC700/IC701 `SE074` are currently treated as likely 3-channel injector pre-driver / driver-logic ICs.
- IC5/IC800 `SE123` are currently treated as probable Denso multi-channel low-side / open-collector style output drivers.

## Confidence Convention

- **Confirmed**: measured continuity, directly observed wiring, or verified firmware behavior.
- **Strong hypothesis**: multiple independent clues agree, but no full functional proof yet.
- **Working hypothesis**: plausible explanation used for analysis, still needs confirmation.
- **Open**: known but not yet understood.

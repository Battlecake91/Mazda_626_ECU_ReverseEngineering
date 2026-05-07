# KLDE ECU Reverse Engineering

Reverse-engineering notes for a Mazda KL-series / KLDE-related Denso ECU.

## Known identifiers

| Marking / identifier | Context | Confidence |
|---|---|---:|
| `U2103136866B` | Control Unit marking | High |
| `KL05` | Only readable part of the ECU part/family marking; the rest is scratched | High |
| `079721-3521` | Denso / PCB / sub-board number, likely related to switching stages, voltage dividers, or an interface board | High |
| `X1 = 8 MHz` | Main crystal | High |

## Documentation index

| File | Purpose |
|---|---|
| [`docs/board_overview.md`](docs/board_overview.md) | ECU identity and architecture overview |
| [`docs/ic_inventory.md`](docs/ic_inventory.md) | IC list and passive parts of interest |
| [`docs/ic1_pin_mapping.md`](docs/ic1_pin_mapping.md) | Main controller / custom MCU pin mapping |
| [`docs/external_bus_memory.md`](docs/external_bus_memory.md) | External address/data bus, ROM/RAM wiring, memory map assumptions |
| [`docs/chip_select_glue_logic.md`](docs/chip_select_glue_logic.md) | 74HC138 / 74HC00 chip-select and `/OE` logic |
| [`docs/peripherals.md`](docs/peripherals.md) | HD63BP21P / HD63B40P and peripheral notes |
| [`docs/ic6_ic500_level_shifter.md`](docs/ic6_ic500_level_shifter.md) | Denso `151821-0020` / `D151821-0020` level-shifter/comparator notes |
| [`docs/se123_driver_devices.md`](docs/se123_driver_devices.md) | SE123 output-driver hypothesis and pin mapping |
| [`docs/connectors_and_signals.md`](docs/connectors_and_signals.md) | Connector nets and traced external signals |
| [`docs/ghidra_hc11_setup.md`](docs/ghidra_hc11_setup.md) | Ghidra setup, HC11 processor module, labels, and workflow |
| [`docs/firmware_analysis.md`](docs/firmware_analysis.md) | Current firmware-analysis notes and variable labels |
| [`docs/open_questions.md`](docs/open_questions.md) | Known unknowns and next reverse-engineering targets |

## Current working model

The ECU appears to use a Motorola-style external bus architecture:

- `IC1 = SC402617FN` is the main custom controller / MCU-like bus master.
- `IC11 = 27C256` is a 32 KiB external EPROM.
- `IC10 = TC5564APL` is external SRAM.
- `IC17 = 74HC138` decodes chip-selects from high address lines.
- `IC20 = 74HC00` generates a shared active-low read `/OE` from `R/W` and `E`-like bus signals.
- `IC4 = HD63BP21P` and `IC7 = HD63B40P` are Motorola-bus-style peripherals.
- `IC6` / `IC500 = 151821-0020` are Denso comparator / 12 V-to-5 V level-shifter ICs.
- `IC5` / `IC800 = SE123` are probably Denso multi-channel output drivers.

The firmware is currently analyzed in Ghidra using an HC11-like processor/language module. This is useful and plausible, but not yet final proof that `IC1` is an off-the-shelf HC11. Naturally, the most important chip wears the least useful marking, because the universe has a sense of humor and it hates documentation.

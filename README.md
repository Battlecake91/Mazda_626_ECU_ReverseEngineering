# KLDE ECU Reverse Engineering

Reverse engineering notes for a Denso-style ECU using an external EPROM, SRAM, peripheral ICs, glue logic and custom driver devices.

This repository collects the current hardware findings, signal tracing notes and working hypotheses. Nothing here should be treated as final unless explicitly marked as verified. This is a reverse-engineering project, not a polished datasheet handed down from the gods, sadly.

## Current focus

- Identify the CPU / main MCU architecture and external bus behavior.
- Map the external address and data bus.
- Decode ROM, RAM and peripheral chip select logic.
- Identify custom Denso ICs and output driver stages.
- Build a reliable memory and I/O map for firmware analysis.
- Correlate Ghidra disassembly with hardware address decoding.

## Main board IC overview

| Reference | Marking / Type | Current interpretation |
|---|---|---|
| IC1 | SC402617FN | Main MCU or custom mask MCU, likely CPU with external bus |
| IC3 | 74HC541 | Octal buffer connected to data bus D0-D7 |
| IC4 | HD63BP21P | PIA-style peripheral interface |
| IC5 | SE123 | Custom Denso multi-channel driver device, chip 1 |
| IC6 | 151821-0020 | Likely custom Denso device |
| IC7 | HD63B40P | Peripheral/timer device, has E/system clock input |
| IC8 | 74HC595 | Serial-in parallel-out shift register |
| IC9 | 74HC74AP | Dual D flip-flop |
| IC10 | TC5564APL | 8 KiB SRAM |
| IC11 | 27C256 | 32 KiB EPROM |
| IC13 | TC4051BP | Analog multiplexer |
| IC17 | 74HC138AP | 3-to-8 decoder, chip select generation |
| IC20 | 74HC00AP | Quad NAND gate, bus control / glue logic |
| IC21 | TLC272 | Dual operational amplifier |
| IC800 | SE123 | Custom Denso multi-channel driver device, chip 2 |
| X1 | 8 MHz crystal | MCU clock source |

## Documentation index

- [Board overview](docs/board_overview.md)
- [IC inventory](docs/ic_inventory.md)
- [Main MCU IC1 pin mapping](docs/ic1_pin_mapping.md)
- [External bus and memory system](docs/external_bus_memory.md)
- [Chip select and glue logic](docs/chip_select_glue_logic.md)
- [Peripheral devices](docs/peripherals.md)
- [SE123 driver devices](docs/se123_driver_devices.md)
- [Connector and signal notes](docs/connectors_and_signals.md)
- [Firmware analysis notes](docs/firmware_analysis.md)
- [Open questions and next steps](docs/open_questions.md)

## Conventions

- `Adr0..Adr14` are external address bus lines.
- `IO0..IO7` are external data bus lines.
- Connector names such as `A1`, `B8`, `C20`, `D19` refer to ECU external connector pins unless otherwise noted.
- Signals with `/` prefix are active-low, for example `/OE`, `/CE`.
- `n.b.` means not populated.
- `F1..F4` are currently treated as trace jumpers, filter elements, fusible links or similar series elements until identified.

## Confidence levels

- **Verified:** confirmed by continuity tracing, datasheet pinout and consistent circuit context.
- **Likely:** strongly suggested by tracing and known IC behavior.
- **Hypothesis:** plausible but still needs measurement or additional tracing.

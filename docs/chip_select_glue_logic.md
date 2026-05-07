# Chip Select and Glue Logic

## Main Devices

| Refdes | Device | Function |
|---|---|---|
| `IC17` | `74HC138AP` | Address decoder. |
| `IC20` | `74HC00AP` | Quad NAND gate used for read-enable generation and signal conditioning. |
| `F1-F4` | Unknown links / filter / fusible / series parts | Used in bus-control and timer-clock paths. Exact part type unknown. |

## IC17 74HC138 Address Decoder

`IC17` decodes external address ranges for peripherals and RAM. Known relevant outputs:

| IC17 pin | 74HC138 output | Destination / role |
|---:|---|---|
| 15 | `/Y0` | `IC7` timer select, range `0x2000-0x23FF`. |
| 14 | `/Y1` | `IC4` PIA select, range `0x2400-0x27FF`. |
| 13 | `/Y2` | `IC3` 74HC541 `/OE1`, range `0x2800-0x2BFF`. |

`IC3 pin1 (/OE1)` is connected to `IC17 pin13`, so the input buffer is address-selected by the decoder.

## IC20 74HC00 Gate Mapping

### Gate 1: Read Output Enable Generation

| IC20 pin | Function | Connected net |
|---:|---|---|
| 1 | Gate 1 input A | `R/W` net: `IC10:27`, `IC4:21`, `IC7:13`, through `F3` to `IC1:47`. |
| 2 | Gate 1 input B | Bus enable / `E` / CE timing net: `IC10:26`, `IC4:25`, `IC7:17`, `IC9:11`, through `F4` to `IC1:48`. |
| 3 | Gate 1 output | Global active-low `/OE` via `F2` to `IC10:22`, `IC11:22`, `IC3:19`. |

Current interpretation:

- `IC20:3` generates a shared active-low output enable for read cycles.
- It enables ROM, RAM, and `IC3` outputs only during valid read bus timing.
- The exact polarity depends on the MCU's `R/W` and `E` conventions, but the observed shared destination is clear.

### Gate 2: Input Conditioning / Inverter

| IC20 pin | Function | Connection |
|---:|---|---|
| 4 | Gate 2 input A | Tied to pin 5, connected through `R101` to `IC6:22`; with `R99` pull-up and `R100` pull-down context. |
| 5 | Gate 2 input B | Tied to pin 4. |
| 6 | Gate 2 output | Connected to `IC1:19` (`A10` pin also listed in address mapping context, so verify net naming carefully). |

Current interpretation:

- Gate 2 is wired as an inverter / Schmitt-like digital conditioning stage.
- Input comes from the `IC6:22` comparator / level-shifter side through surrounding resistors.
- Output reaches `IC1:19`. This needs careful schematic interpretation because `IC1:19` is also currently documented as address line `A10`; this may reflect net reuse confusion or measurement context that should be revisited.

### Gate 3: Unresolved / Partial

| IC20 pin | Function | Known connection |
|---:|---|---|
| 8 | Gate 3 output | Through `R606` to `IC1:21`. |
| 9 | Gate 3 input | Connected to pin 10 and `C10`. |
| 10 | Gate 3 input | Connected to pin 9 and `C10`. |

Current interpretation:

- Gate 3 is likely used as an inverter / RC conditioning stage.
- Exact role unresolved.

### Gate 4: Decoder Enable / Control

| IC20 pin | Function | Connection |
|---:|---|---|
| 11 | Gate 4 output | Connected to `IC11:20`. Earlier assumptions about this net should be rechecked. |
| 12 | Gate 4 input A | Tied to pin 13 and connected to `IC17:4` (`/G2A` or enable input context). |
| 13 | Gate 4 input B | Tied to pin 12. |

Important correction:

- `IC20:12/13` are the inputs.
- `IC20:11` is the NAND gate output.
- Earlier assumptions that pin 12 was an output to `IC17:4` are incorrect.

## Shared `/OE` Net

Known destinations of the shared active-low output enable net:

| Device | Pin | Role |
|---|---:|---|
| `IC10` SRAM | 22 | `/OE` |
| `IC11` ROM | 22 | `/OE` |
| `IC3` 74HC541 | 19 | `/OE2` |

This means `IC3` is enabled only when both conditions are true:

1. Address select via `IC17` on `IC3 pin1 (/OE1)`.
2. Read-cycle global `/OE` via `IC20:3` on `IC3 pin19 (/OE2)`.

That is pleasantly sane, which is suspicious, but useful.

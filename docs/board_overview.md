# Board Overview

## ECU / PCB identification

| Item | Value | Notes | Confidence |
|---|---|---|---:|
| Control Unit marking | `U2103136866B` | Marking found on the ECU / control unit | High |
| ECU readable family mark | `KL05` | Only this part is readable; the remaining serial / part number is scratched | High |
| Denso / PCB / sub-board number | `079721-3521` | Likely related to switching stages, voltage dividers, or an interface sub-board | High |
| Crystal | `8 MHz` | `X1` | High |

## Architecture summary

The board appears to be a Denso ECU with a custom main controller and an external 8-bit bus.

Known bus devices:

| Device | RefDes | Function |
|---|---|---|
| Main controller / custom MCU | `IC1 = SC402617FN` | Main CPU/controller, external bus master |
| ROM | `IC11 = 27C256` | 32 KiB EPROM / firmware ROM |
| RAM | `IC10 = TC5564APL` | 8 KiB SRAM |
| Peripheral / PIA | `IC4 = HD63BP21P` | Parallel I/O device, Motorola bus style |
| Peripheral / timer | `IC7 = HD63B40P` | Timer / peripheral device, Motorola bus style |
| Bus buffer | `IC3 = 74HC541` | External input buffer onto the data bus |
| Address decoder | `IC17 = 74HC138AP` | Chip-select generation |
| Glue logic | `IC20 = 74HC00AP` | Read-output-enable and additional logic |

## Functional blocks

| Block | ICs / parts | Current interpretation |
|---|---|---|
| CPU / bus master | `IC1` | Custom MCU or ASIC using external ROM/RAM and memory-mapped I/O |
| Program memory | `IC11` | 27C256, likely full 32 KiB firmware image |
| External RAM | `IC10` | TC5564, likely variables, stack, and buffers |
| Address decoding | `IC17`, `IC20` | 74HC138 plus NAND glue logic |
| Input conditioning | `IC6`, `IC500` | Denso comparator / 12 V-to-5 V level-shifter ICs |
| Output stages | `IC5`, `IC800` | SE123, likely multi-channel low-side/open-collector-style drivers |
| Analog mux / op amp | `IC13`, `IC21` | Sensor multiplexing / analog conditioning |
| Logic / latch / buffer | `IC3`, `IC8`, `IC9`, `IC20` | Bus buffer, shift register, flip-flop, glue logic |

## Documentation conventions

- `ICx:pin` means reference designator and pin number.
- `A0..A14` are external address-bus lines unless explicitly stated otherwise.
- `IO0..IO7` are external data-bus lines.
- `A1..A20`, `B1..B20`, `C1..C20`, `D1..D20` are ECU connector pins unless context says otherwise.
- Active-low signals use `/`, for example `/OE`, `/CE`, `/Y5`.
- `n.b.` means not populated.

## Caution

Several parts are Denso-custom or poorly documented. These notes deliberately separate traced facts from hypotheses, because one confident wrong assumption can eat a weekend and then ask for dessert.

# IC1 Main MCU Pin Mapping

IC1 is marked `SC402617FN`. It is currently treated as the main MCU or custom CPU.

## Address bus pins

| IC1 pin | Signal |
|---:|---|
| 15 | Adr14 |
| 16 | Adr13 |
| 17 | Adr12 |
| 18 | Adr11 |
| 19 | Adr10 / also connected to IC20 pin 6 context |
| 20 | Adr9 |
| 21 | Adr8 / also connected through R606 to IC20 pin 8 |
| 71 | Adr7 |
| 72 | Adr6 |
| 73 | Adr5 |
| 74 | Adr4 |
| 75 | Adr3 |
| 76 | Adr2 |
| 77 | Adr1 |
| 78 | Adr0 |

## Data bus pins

| IC1 pin | Signal |
|---:|---|
| 49 | IO0 |
| 50 | IO1 |
| 51 | IO2 |
| 52 | IO3 |
| 53 | IO4 |
| 54 | IO5 |
| 55 | IO6 |
| 56 | IO7 |

## Power, ground and clock-related pins

| IC1 pin | Signal / note |
|---:|---|
| 22 | VCC |
| 23 | GND |
| 40 | GND |
| 41 | VCC |
| 44 | XTAL |
| 45 | XTAL |
| 46 | GND |
| 66 | VCC |
| 83 | VCC |

## Bus/control-related pins

| IC1 pin | Connected to | Current interpretation |
|---:|---|---|
| 47 | Through F3 to IC20 pin 1, IC10 pin 27, IC4 pin 21, IC7 pin 13 | `R/W`-like bus control |
| 48 | Through F4 to IC20 pin 2, IC10 pin 26, IC4 pin 25, IC7 pin 17 | `E` / bus enable / system clock-like control |

## SE123-related output/control pins

| IC1 pin | Connected to |
|---:|---|
| 25 | IC800 pin 4 |
| 27 | IC800 pin 5 |
| 28 | IC800 pin 6 |
| 57 | IC800 pin 8 |
| 58 | IC800 pin 9 |
| 59 | IC800 pin 10 |
| 60 | IC5 pin 4 |
| 80 | IC5 pin 5 |

## Notes

- Address lines `Adr0..Adr14` support a 32 KiB EPROM address space directly.
- Data lines `IO0..IO7` are shared by ROM, RAM and bus peripherals.
- `IC1 pin 47` and `IC1 pin 48` are critical for reconstructing bus cycles.
- The `SC402617FN` marking suggests a custom or mask-programmed part rather than a convenient textbook MCU. Naturally, because easy things are illegal in old ECUs.

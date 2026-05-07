# IC Inventory

## Main ECU Board

| Refdes | Marking / part | Type | Role | Confidence |
|---|---|---|---|---|
| `IC1` | `SC402617FN` | MCU / custom MCU | Main controller, external bus master. | Strong hypothesis / confirmed connections. |
| `IC3` | `74HC541` | Octal buffer | Memory-mapped input port on data bus `D0-D7`. | Confirmed. |
| `IC4` | `HD63BP21P` | PIA | Parallel I/O adapter mapped at `0x2400-0x27FF`. | Confirmed device, pin mapping partly active. |
| `IC5` | `SE123` | Denso custom | Multi-channel driver / interface IC. | Strong hypothesis. |
| `IC6` | `151821-0020` / `D151821-0020` | Denso custom comparator / level shifter | 12 V / ground detect / comparator interface to 5 V logic. | Strong hypothesis with source and behavior notes. |
| `IC7` | `HD63B40P` | Programmable timer | Timer outputs likely used for injection / pulse timing. | Confirmed device and select pins. |
| `IC8` | `74HC595` | Shift register | Output latch / serial expansion. | Confirmed part, role pending. |
| `IC9` | `74HC74AP` | Dual D flip-flop | Two-stage divider generating timer clock from bus enable / E. | Confirmed. |
| `IC10` | `TC5564APL` | 8 KiB SRAM | External RAM, selected at `0x2C00-0x2FFF` window. | Confirmed. |
| `IC11` | `27C256` | 32 KiB EPROM | External ROM, mapped at `0x8000-0xFFFF` area. | Confirmed. |
| `IC13` | `TC4051BP` | Analog mux | Analog input selection. | Confirmed part, full routing pending. |
| `IC17` | `74HC138AP` | 3-to-8 decoder | Chip select decoder. | Confirmed. |
| `IC20` | `74HC00AP` | Quad NAND | Read enable and reset / input conditioning glue logic. | Confirmed. |
| `IC21` | `TLC272` | Dual op amp | Analog conditioning. | Confirmed part, full routing pending. |
| `IC800` | `SE123` | Denso custom | Second multi-channel driver / interface IC. | Strong hypothesis. |

## Daughterboard / Interface Board

| Refdes | Marking / part | Supply pins known | Current role |
|---|---|---|---|
| `IC001` | `SE134` | `+5 V` pin 7, GND pin 6, `+12 V` pins 10 and 11 | Denso custom interface IC. Exact function unknown. |
| `IC250` | `MP611` | `+5 V` pin 12, GND pins 3 and 15 | Denso custom IC. Connected to shared SE074 pins 1/2 through pin 19. |
| `IC500` | `D151821-0020` | See level shifter notes | Comparator / level shifter, same family as `IC6`. |
| `IC700` | `SE074` | `+5 V` pin 16, GND pins 3, 8, 14, `+12 V` pin 9 | Likely injector pre-driver / driver logic. |
| `IC701` | `SE074` | `+5 V` pins 3, 14, 16, GND pin 8, `+12 V` pin 9 | Likely injector pre-driver / driver logic. |

## Clock

| Refdes | Value | Notes |
|---|---:|---|
| `X1` | 8 MHz | Main oscillator / crystal. |

## Important Corrections

- `IC4` register select is not the initially assumed order. Current confirmed mapping: `IC4 pin35 RS1 = A0`, `IC4 pin36 RS0 = A1`. This mirrors the apparent PIA register addresses compared to the simple datasheet order.
- `IC7` register select is normal: `pin10 RS0 = A0`, `pin11 RS1 = A1`, `pin12 RS2 = A2`.
- `IC7` pins `4`, `7`, and `28` are timer clock inputs and are tied together through `F1` to `IC9 pin5`.
- `IC20 pin11`, not pin12, is the output of NAND gate 4. Pins `12` and `13` are tied inputs feeding that gate.

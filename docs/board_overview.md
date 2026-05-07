# Board Overview

## Scope

These notes describe the known hardware structure of the KLDE / KL05 Denso ECU assembly currently under reverse engineering.

The board set consists of at least the main ECU logic board and a secondary interface / power-stage related board. The secondary board carries additional Denso custom ICs and transistor output stages. The exact official name of the daughterboard is unknown.

## Identifiers

| Identifier | Meaning / location | Confidence |
|---|---|---|
| `U2103136866B` | Control unit identifier. | Confirmed marking. |
| `KL05` | Remaining readable part of the ECU marking. Other serial / part number text is damaged. | Confirmed marking. |
| `079721-3521` | Denso / PCB / subassembly number on the interface or power-stage related board. | Confirmed marking. |

## Main Board ICs

| Refdes | Device / marking | Current interpretation |
|---|---|---|
| `IC1` | `SC402617FN` | Main MCU or custom MCU, HC11-like external bus context. |
| `IC3` | `74HC541` | 8-bit input buffer / input port mapped to external data bus. |
| `IC4` | `HD63BP21P` | PIA, mapped at `0x2400-0x27FF`. |
| `IC5` | `SE123` | Denso custom multi-channel driver / interface IC. |
| `IC6` | `151821-0020` / `D151821-0020` | Denso comparator / 12 V to 5 V level shifter with mixed channel behavior. |
| `IC7` | `HD63B40P` | Programmable timer, mapped at `0x2000-0x23FF`. |
| `IC8` | `74HC595` | Serial-in / parallel-out latch or output expansion. |
| `IC9` | `74HC74AP` | Dual D flip-flop used as a two-stage clock divider for `IC7`. |
| `IC10` | `TC5564APL` | External SRAM. |
| `IC11` | `27C256` | External EPROM / ROM. |
| `IC13` | `TC4051BP` | Analog multiplexer. |
| `IC17` | `74HC138AP` | Address decoder for external peripheral / RAM windows. |
| `IC20` | `74HC00AP` | NAND glue logic, including global read `/OE` generation. |
| `IC21` | `TLC272` | Dual operational amplifier. |
| `IC800` | `SE123` | Second Denso custom multi-channel driver / interface IC. |
| `X1` | 8 MHz crystal | MCU clock source. |

## Daughterboard / Interface Board ICs

| Refdes | Marking | Current interpretation |
|---|---|---|
| `IC001` | `SE134` | Denso custom interface IC. Function unknown. |
| `IC250` | `MP611` | Denso custom interface IC. Function unknown. |
| `IC500` | `151821-0020` / `D151821-0020` | Same family / function as `IC6`, used for level shifting / comparator inputs. |
| `IC700` | `SE074` | Likely 3-channel injector pre-driver / driver logic. |
| `IC701` | `SE074` | Likely 3-channel injector pre-driver / driver logic. |

## Key Working Assumptions

1. `IC1` provides an external 8-bit data bus and at least 15 external address lines.
2. `IC17` decodes external peripheral address windows.
3. `IC20` combines bus control signals and creates an active-low read output-enable signal shared by RAM, ROM, and the `IC3` input port.
4. `IC7` timer output pins are routed to external connector signals and then into `SE074` circuitry on the secondary board.
5. `SE074` devices likely interface the ECU timer / MCU control signals to six injector transistor stages.
6. The US wiring diagram injector numbering is currently treated as less reliable than internal Denso component numbering (`T711`, `T721`, ... `T761`).

## Documentation Rules

- Measurements outrank guesses.
- Datasheet pin names are used where the package pinout is known.
- Conflicting old assumptions are explicitly marked instead of silently removed, because otherwise the same mistake comes back wearing a fake mustache.

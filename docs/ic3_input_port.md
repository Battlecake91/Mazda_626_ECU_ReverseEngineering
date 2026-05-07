# IC3 74HC541 Input Port

## Device

`IC3` is a `74HC541` octal buffer connected to the external data bus. It is treated as a memory-mapped input port.

## Data Bus Side

| IC3 output pin | Data bus bit |
|---:|---|
| 18 | `D0` |
| 17 | `D1` |
| 16 | `D2` |
| 15 | `D3` |
| 14 | `D4` |
| 13 | `D5` |
| 12 | `D6` |
| 11 | `D7` |

## Enable / Output Control

| IC3 pin | Signal | Source / destination |
|---:|---|---|
| 1 | `/OE1` | `IC17:13`, address-select output. |
| 19 | `/OE2` | Shared global read `/OE` net from `IC20:3`, together with `IC10:22` and `IC11:22`. |

Current interpretation:

- `IC17` selects the input port address range.
- `IC20` ensures the buffer only drives the bus during read cycles.
- Working address range: `0x2800-0x2BFF`.

## Input Side Mapping

| IC3 input | Signal path | Notes |
|---|---|---|
| `A0` | Connector `A5` -> `IC500:3` | Daughterboard level-shifter output. |
| `A1` | Connector `A4` -> `IC500:7` | Daughterboard level-shifter output. |
| `A2` | Connector `B5` -> `IC500:8` | Daughterboard level-shifter output. |
| `A3` | Connector `B6` -> `IC500:4` | Daughterboard level-shifter output. |
| `A4` | `IC6:8` | Main-board level-shifter output. |
| `A5` | Connector `B7` -> `IC500:6` | Daughterboard level-shifter output. |
| `A6` | Connector `A6` -> `IC500:10` | Daughterboard level-shifter output. |
| `A7` | Connector `B9` -> `IC500:11` | Daughterboard level-shifter output. |

## Connector Naming Convention

Connector references use the form `A5`, `B7`, etc. Example: connector row/side `A`, pin `5` = `A5`.

## Interpretation

Most `IC3` inputs come from `IC500`, which is a Denso `D151821-0020` level-shifter / comparator IC on the secondary board. Therefore `IC3` likely reads conditioned external 12 V / ground / comparator signals as an 8-bit software-visible input byte.

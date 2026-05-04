# Connectors and Signal Notes

## Connector naming

The ECU connector pins are named by letter and number:

- `A1..A20`
- `B1..B20`
- `C1..C20`
- `D1..D20`

Example: `A3` means connector block A, pin 3.

## Flat-flex / second-board connection notes

`IC3 74HC541` receives several inputs from another board through a connector. That second board contains `IC500`, which appears similar or identical to `IC6`.

| IC3 input | Connector path | Destination |
|---|---|---|
| A0 | A5 | IC500 pin 3 |
| A1 | A4 | IC500 pin 7 |
| A2 | B5 | IC500 pin 8 |
| A3 | B6 | IC500 pin 4 |
| A4 | Local | IC6 pin 8 |
| A5 | B7 | IC500 pin 6 |
| A6 | A6 | IC500 pin 10 |
| A7 | B9 | IC500 pin 11 |

Because `IC3` outputs to the data bus and is enabled by address decode plus read-enable, these signals likely appear as a memory-mapped input byte.

## SE123 connector outputs

### IC5 output-side paths

| IC5 pin | External / local net |
|---:|---|
| 14 | C4 |
| 15 | C11 |
| 16 | C12 |
| 17 | D16 |
| 18 | D14 |
| 19 | A10 |
| 20 | B12 |
| 21 | A1 |
| 22 | Not connected |
| 23 | Not connected |

### IC800 output-side paths

| IC800 pin | External / local net |
|---:|---|
| 14 | C4 |
| 15 | D12 |
| 16 | D19 |
| 17 | D18 |
| 18 | A9 |
| 19 | C20 |
| 20 | D13 |
| 21 | D15 |
| 22 | C19 |
| 23 | Not connected |

## Notable external paths

| Net | Notes |
|---|---|
| A1 | Has SOT23-like transistor marked `23` with underline |
| C11 | Has unpopulated resistor to transistor, then external connector path |
| C12 | Has `4.3k` resistor and transistor, then external connector path |
| C4 | Shared by IC5 pin 14 and IC800 pin 14, also goes to SE074 pin 15 |
| B8 | Connected to IC4 pin 16 |

## Known resistor and capacitor context

### 0805 resistors, 10k

`R68, R59, R64, R65, R66, R67, R70, R71, R69, R60, R61, R62, R63, R4, R58, R3, R57, R6, R10, R11, R9, R8, R7, R2, R13, R18, R14, R48, R49, R50, R52, R55, R51, R53, R54, R73, R97, R98, R22, R23, R30, R19, R17, R16, R21, R155, R38, R39, R40, R36, R35, R34, R33, R605, R25, R31, R32, R28, R29, R15, R87, R85, R75, R20, R5, R82, R83, R84, R601, R602, R56, R613, R611`

### Other known resistors

| Reference | Value / note |
|---|---|
| R606 | 1k 0805 |
| R44, R45, R46, R47, R96 | 1k 0805 |
| R95 | 75k 0805 |
| R612 | 75k 0805 |
| R43, R99, R100 | 5.1k 0805 |
| R1 | 4.7k 0805 |
| R607 | 1M 0805 |
| R101 | 43k 0805 |
| R37, R42, R41 | Not populated, 0805 footprint |
| R891 | 330 ohm 0805 |
| RA1 | Resistor network, 16B25K/50K |

### Known / unknown capacitors

| Reference | Note |
|---|---|
| C95 | 4.7 uF tantalum |
| C17 | 0805, not populated |
| C20, C21, C4 | 0805, unknown |
| C5, C132, C137 | 0805, unknown |
| C35, C31, C34, C3, C23, C25, C14, C999, C13, C36, C9, C6, C10, C7, C22, C133, C131, C27, C16, C18, C19, C134, C135, C15, C080, C1, C2 | Unknown 0805 capacitors |
| C992, C989, C998, C995, C996, C991, C990 | Not populated |

Note: `C999` was mentioned twice in source notes.

### SOT23-like parts with `C3` marking

`D133, D131, D132, D134, D135, D137, D95`

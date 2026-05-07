# Component / BOM Notes

## Purpose

This file collects known component values and placement notes. It is not yet a full BOM export. It is the reverse-engineering version of a BOM: part science plus mild despair.

## Capacitors

### Confirmed Special Values

| Refdes | Value / status | Package / type | Notes |
|---|---|---|---|
| `C1` | 40 pF | 0805 C0G | Likely oscillator / timing context. |
| `C2` | 40 pF | 0805 C0G | Likely oscillator / timing context. |
| `C95` | 4.7 µF | Tantalum | Known populated tantalum. |

### Confirmed Not Fitted / Not Populated

| Refdes | Status |
|---|---|
| `C17` | n.f. / not fitted in earlier main-board capacitor context. Note: external signal `C17` also exists as connector name; do not confuse. |
| `C989` | n.f. |
| `C990` | n.f. |
| `C991` | n.f. |
| `C992` | n.f. |
| `C995` | n.f. |
| `C996` | n.f. |
| `C997` | n.f. |
| `C998` | n.f. |

### Previously Unknown Populated 0805 Capacitors

All previously unknown populated 0805 capacitors, except `C1` and `C2`, are now treated as `100 nF`.

This applies to the previously unknown populated set including:

`C35`, `C31`, `C34`, `C3`, `C23`, `C25`, `C14`, `C999`, `C13`, `C36`, `C9`, `C6`, `C10`, `C7`, `C22`, `C133`, `C131`, `C27`, `C16`, `C18`, `C19`, `C134`, `C135`, `C15`, `C080`.

`C999` was mentioned twice in earlier notes; keep only one BOM entry.

## Resistors

### 10 kΩ 0805

Known `10 kΩ` 0805 resistors:

`R29`, `R15`, `R87`, `R85`, `R75`, `R20`, `R5`, `R82`, `R83`, `R84`, `R601`, `R602`, `R56`, `R613`, `R611`, `R68`, `R59`, `R64`, `R65`, `R66`, `R67`, `R70`, `R71`, `R69`, `R60`, `R61`, `R62`, `R63`, `R4`, `R58`, `R3`, `R57`, `R6`, `R10`, `R11`, `R9`, `R8`, `R7`, `R2`, `R13`, `R18`, `R14`, `R48`, `R49`, `R50`, `R52`, `R55`, `R51`, `R53`, `R54`, `R73`, `R97`, `R98`, `R22`, `R23`, `R30`, `R19`, `R17`, `R16`, `R21`, `R155`, `R38`, `R39`, `R40`, `R36`, `R35`, `R34`, `R33`, `R605`, `R25`, `R31`, `R32`, `R28`.

### 1 kΩ 0805

`R606`, `R44`, `R45`, `R46`, `R47`, `R96`.

### 5.1 kΩ 0805

`R43`, `R99`, `R100`.

### 75 kΩ 0805

`R95`, `R612`.

### Other Known Values

| Refdes | Value | Package | Notes |
|---|---:|---|---|
| `R1` | 4.7 kΩ | 0805 | Known. |
| `R101` | 43 kΩ | 0805 | In `IC20` gate 2 / `IC6:22` conditioning context. |
| `R607` | 1 MΩ | 0805 | Known. |
| `R891` | 330 Ω | 0805 | Known. |
| `RA1` | 16B25K / 50K | resistor network | Exact network interpretation pending. |

### Not Fitted / Unknown Resistors

| Refdes | Status |
|---|---|
| `R37` | n.b. / not populated or unknown in earlier notes. |
| `R41` | n.b. / not populated or unknown in earlier notes. |
| `R42` | n.b. / not populated or unknown in earlier notes. |

Note: Some current PIA notes mention `R37`, `R41`, `R42` as pull-down / coding parts. Verify population state on the actual board. This is a known documentation conflict caused by incremental measurements.

## Diodes / SOT23-like Parts

SOT23-like devices with `C3` marking:

`D133`, `D131`, `D132`, `D134`, `D135`, `D137`, `D95`.

Other SOT23-like note:

- A transistor near external `A1` has marking `23` with an underline.

## Unknown Links / Filter Parts

| Refdes | Notes |
|---|---|
| `F1` | In timer clock path from `IC9:5` to `IC7:4/7/28`. |
| `F2` | In global `/OE` path from `IC20:3` to ROM/RAM/IC3 `/OE`. |
| `F3` | In `R/W` path from `IC1:47` to bus logic. |
| `F4` | In `E` / bus-enable path from `IC1:48` to bus logic. |

Exact part type of `F1-F4` is still unknown.

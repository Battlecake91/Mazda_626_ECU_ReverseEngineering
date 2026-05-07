# IC Inventory

## Main IC list

| RefDes | Part marking | Known / likely function | Confidence |
|---|---|---|---:|
| `IC1` | `SC402617FN` | Main MCU or Denso custom controller | Medium |
| `IC3` | `74HC541` | Octal tri-state input buffer onto data bus | High |
| `IC4` | `HD63BP21P` | PIA-style parallel I/O peripheral | High |
| `IC5` | `SE123` | Denso custom multi-channel output driver | Medium |
| `IC6` | `151821-0020` / `D151821-0020` | Denso comparator / level-shifter IC | Medium-High |
| `IC7` | `HD63B40P` | Motorola-style timer/peripheral IC | High |
| `IC8` | `74HC595` | Serial-in parallel-out shift register | High |
| `IC9` | `74HC74AP` | Dual D flip-flop | High |
| `IC10` | `TC5564APL` | 8 KiB SRAM | High |
| `IC11` | `27C256` | 32 KiB EPROM / firmware ROM | High |
| `IC13` | `TC4051BP` | 8-channel analog multiplexer | High |
| `IC17` | `74HC138AP` | 3-to-8 decoder / chip-select logic | High |
| `IC20` | `74HC00AP` | Quad NAND glue logic | High |
| `IC21` | `TLC272` | Dual op amp | High |
| `IC800` | `SE123` | Denso custom multi-channel output driver | Medium |
| `X1` | `8 MHz` | Main crystal | High |

## Passive / surrounding parts of interest

### SE123 / output-stage neighborhood

| Part | Value / marking | Notes |
|---|---|---|
| `R29`, `R15`, `R87`, `R85`, `R75`, `R20`, `R5`, `R82`, `R83`, `R84`, `R601`, `R602`, `R56`, `R613`, `R611` | `10 kΩ`, 0805 | Around SE123 / related logic |
| `R891` | `330 Ω`, 0805 | Near output circuitry |
| `R612` | `75 kΩ`, 0805 | Near output circuitry |
| `RA1` | `16B25K/50K` | Resistor network |
| `C95` | `4.7 µF`, tantalum | Supply / filtering |
| `C17` | not populated | 0805 footprint |
| `C20`, `C21`, `C4` | unknown | 0805 capacitors |

### Other known resistor groups

| Value | Parts |
|---|---|
| `10 kΩ`, 0805 | `R68`, `R59`, `R64`, `R65`, `R66`, `R67`, `R70`, `R71`, `R69`, `R60`, `R61`, `R62`, `R63`, `R4`, `R58`, `R3`, `R57`, `R6`, `R10`, `R11`, `R9`, `R8`, `R7`, `R2`, `R13`, `R18`, `R14`, `R48`, `R49`, `R50`, `R52`, `R55`, `R51`, `R53`, `R54`, `R73`, `R97`, `R98`, `R22`, `R23`, `R30`, `R19`, `R17`, `R16`, `R21`, `R155`, `R38`, `R39`, `R40`, `R36`, `R35`, `R34`, `R33`, `R605`, `R25`, `R31`, `R32`, `R28` |
| `1 kΩ`, 0805 | `R606`, `R44`, `R45`, `R46`, `R47`, `R96` |
| `75 kΩ`, 0805 | `R95` |
| `5.1 kΩ`, 0805 | `R43`, `R99`, `R100` |
| `4.7 kΩ`, 0805 | `R1` |
| `1 MΩ`, 0805 | `R607` |
| `43 kΩ`, 0805 | `R101` |
| not populated | `R37`, `R42`, `R41` |

### Unknown / not-yet-measured capacitors

Unknown 0805 capacitors currently noted:

`C35`, `C31`, `C34`, `C3`, `C23`, `C25`, `C14`, `C999`, `C13`, `C36`, `C9`, `C6`, `C10`, `C7`, `C22`, `C133`, `C131`, `C27`, `C16`, `C18`, `C19`, `C134`, `C135`, `C15`, `C080`, `C1`, `C2`.

Not populated:

`C992`, `C989`, `C998`, `C995`, `C996`, `C991`, `C990`.

### SOT23-like parts with `C3` marking

`D133`, `D131`, `D132`, `D134`, `D135`, `D137`, `D95`.

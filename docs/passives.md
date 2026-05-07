# Passive Components and Small Devices

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Resistors
### 10 kOhm, 0805

| RefDes |
|---|
| R29 |
| R15 |
| R87 |
| R85 |
| R75 |
| R20 |
| R5 |
| R82 |
| R83 |
| R84 |
| R601 |
| R602 |
| R56 |
| R613 |
| R611 |
| R68 |
| R59 |
| R64 |
| R65 |
| R66 |
| R67 |
| R70 |
| R71 |
| R69 |
| R60 |
| R61 |
| R62 |
| R63 |
| R4 |
| R58 |
| R3 |
| R57 |
| R6 |
| R10 |
| R11 |
| R9 |
| R8 |
| R7 |
| R2 |
| R13 |
| R18 |
| R14 |
| R48 |
| R49 |
| R50 |
| R52 |
| R55 |
| R51 |
| R53 |
| R54 |
| R73 |
| R97 |
| R98 |
| R22 |
| R23 |
| R30 |
| R19 |
| R17 |
| R16 |
| R21 |
| R155 |
| R38 |
| R39 |
| R40 |
| R36 |
| R35 |
| R34 |
| R33 |
| R605 |
| R25 |
| R31 |
| R32 |
| R28 |

### Other Known Resistors

| RefDes | Value | Package | Notes |
|---|---:|---|---|
| R606 | 1 kOhm | 0805 | IC20:8 to IC1:11 path. |
| R44 | 1 kOhm | 0805 |  |
| R45 | 1 kOhm | 0805 |  |
| R46 | 1 kOhm | 0805 |  |
| R47 | 1 kOhm | 0805 |  |
| R96 | 1 kOhm | 0805 |  |
| R95 | 75 kOhm | 0805 |  |
| R612 | 75 kOhm | 0805 |  |
| R43 | 5.1 kOhm | 0805 | Part of IC6/IC20 Schaltung 1 area. |
| R99 | 5.1 kOhm | 0805 | Pull-up at IC6:22 in Schaltung 1. |
| R100 | 5.1 kOhm | 0805 | Pull-down at IC6:22 in Schaltung 1. |
| R1 | 4.7 kOhm | 0805 |  |
| R607 | 1 MOhm | 0805 |  |
| R101 | 43 kOhm | 0805 | Schaltung 1 coupling / divider path. |
| R891 | 330 Ohm | 0805 |  |
| R37 | n.b. | 0805 | Not populated. |
| R41 | n.b. | 0805 | Not populated. |
| R42 | n.b. | 0805 | Not populated. |
| RA1 | 16B25K / 50K | resistor network | Exact network function not fully mapped. |

---


## Capacitors
### Known Values

| RefDes | Value / Status | Package / Type | Notes |
|---|---|---|---|
| C1 | 40 pF | 0805 C0G | Likely crystal load capacitor. |
| C2 | 40 pF | 0805 C0G | Likely crystal load capacitor. |
| C95 | 4.7 uF | Tantalum | Power / local reservoir capacitor. |

### General Rule for Previously Unknown Populated 0805 Capacitors

All previously unknown populated 0805 capacitors, except C1 and C2, are now treated as:

| Group | Value | Package |
|---|---:|---|
| Populated unknown 0805 capacitors except C1/C2 | 100 nF | 0805 |

This includes, unless otherwise noted:

| RefDes | Value | Package |
|---|---:|---|
| C35 | 100 nF | 0805 |
| C31 | 100 nF | 0805 |
| C34 | 100 nF | 0805 |
| C3 | 100 nF | 0805 |
| C23 | 100 nF | 0805 |
| C25 | 100 nF | 0805 |
| C14 | 100 nF | 0805 |
| C999 | 100 nF | 0805 |
| C13 | 100 nF | 0805 |
| C36 | 100 nF | 0805 |
| C9 | 100 nF | 0805 |
| C6 | 100 nF | 0805 |
| C10 | 100 nF | 0805 |
| C7 | 100 nF | 0805 |
| C22 | 100 nF | 0805 |
| C133 | 100 nF | 0805 |
| C131 | 100 nF | 0805 |
| C27 | 100 nF | 0805 |
| C16 | 100 nF | 0805 |
| C18 | 100 nF | 0805 |
| C19 | 100 nF | 0805 |
| C134 | 100 nF | 0805 |
| C135 | 100 nF | 0805 |
| C15 | 100 nF | 0805 |
| C080 | 100 nF | 0805 |
| C4 | 100 nF | 0805 |
| C20 | 100 nF | 0805 |
| C21 | 100 nF | 0805 |
| C5 | 100 nF | 0805 |
| C132 | 100 nF | 0805 |
| C137 | 100 nF | 0805 |

### Not Fitted / Not Populated Capacitors

| RefDes | Status |
|---|---|
| C17 | n.f. / not fitted |
| C989 | n.f. / not fitted |
| C990 | n.f. / not fitted |
| C991 | n.f. / not fitted |
| C992 | n.f. / not fitted |
| C995 | n.f. / not fitted |
| C996 | n.f. / not fitted |
| C997 | n.f. / not fitted |
| C998 | n.f. / not fitted |

---


## Diodes / SOT-23 Devices
| RefDes | Marking | Package | Notes |
|---|---|---|---|
| D133 | C3 | SOT-23-like | Unknown exact part. |
| D131 | C3 | SOT-23-like | Unknown exact part. |
| D132 | C3 | SOT-23-like | Unknown exact part. |
| D134 | C3 | SOT-23-like | Unknown exact part. |
| D135 | C3 | SOT-23-like | Unknown exact part. |
| D137 | C3 | SOT-23-like | Unknown exact part. |
| D95 | C3 | SOT-23-like | Unknown exact part. |

---

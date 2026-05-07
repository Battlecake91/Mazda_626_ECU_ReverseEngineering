# Chip Select and Glue Logic

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## IC17 74HC138 Decoder
| IC17 Pin | 74HC138 Signal | Connected To | Notes |
|---:|---|---|---|
| 1 | A0 | A10 | Decoder input bit 0. |
| 2 | A1 | A11 | Decoder input bit 1. |
| 3 | A2 | A12 | Decoder input bit 2. |
| 4 | `/E1` | IC1:14, IC20:12/13, Pull-up R56 to VCC/IC20:14 | Active-low enable. Also drives ROM /CE inverter through IC20 gate 4. |
| 5 | `/E2` | A14, Pull-up R57 | Active-low enable. |
| 6 | E3 | A13, Pull-up R58 | Active-high enable. |
| 7 | `/Y7` | n.c. | Not wired. |
| 9 | `/Y6` | n.c. | Not wired. |
| 10 | `/Y5` | n.c. | Not wired. |
| 11 | `/Y4` | n.c. | Not wired. |
| 12 | `/Y3` | IC10 Pin 20 `/CE1` | External RAM window `0x2C00-0x2FFF`. |
| 13 | `/Y2` | IC3 Pin 1 `/OE1` | Input port window `0x2800-0x2BFF`. |
| 14 | `/Y1` | IC4 Pin 23 `/CS2` | PIA window `0x2400-0x27FF`. |
| 15 | `/Y0` | IC7 Pin 15 `/CS0` | Timer/peripheral window `0x2000-0x23FF`. |

IC17 is active when: `IC1:14 = 0`, `A14 = 0`, `A13 = 1`.

---


## IC20 74HC00 Bus / Select Logic
| Gate | IC20 Pins | Logic | Connections | Inferred Function |
|---|---|---|---|---|
| Gate 1 | 1,2 -> 3 | `NAND(R/W, E)` | Pin 1: R/W net; Pin 2: E net; Pin 3: IC10 `/OE`, IC11 `/OE`, IC3 `/OE2` | Generates global active-low read `/OE`. |
| Gate 2 | 4,5 -> 6 | Inverter | Pins 4+5 connected to IC6-related Schaltung 1; Pin 6 to IC1:9 | Inverts IC6-related status / comparator signal. |
| Gate 3 | 9,10 -> 8 | Inverter | Pins 9+10 to external C10 node; Pin 8 via R606 to IC1:11 | Timing / reset / status signal, unresolved. |
| Gate 4 | 12,13 -> 11 | Inverter | Pins 12+13 to IC1:14 / IC17 `/E1`; Pin 11 to IC11 `/CE` | ROM `/CE`; ROM active when IC1:14 is high. |

### IC20 Pin Connections

| IC20 Pin | Connection |
|---:|---|
| 1 | IC10:27 R/W, IC4:21 `/R/W`, IC7:13 `/R/W`, via F3 to IC1:47 |
| 2 | IC10:26 CE2, IC4:25 E, IC7:17 E, via F4 to IC1:48 |
| 3 | Via F2 to IC10:22 `/OE`, IC11:22 `/OE`, IC3:19 `/OE2` |
| 4+5 | Schaltung 1 / IC6-related node |
| 6 | IC1:9 |
| 8 | R606 -> IC1:11 |
| 9+10 | C10 external node |
| 11 | IC11:20 `/CE` |
| 12+13 | IC1:14, IC17:4 `/E1`, pull-up R56 |
| 14 | VCC |

### Schaltung 1 / IC20 Gate 2

| Node | Connection / Function |
|---|---|
| IC20:4+5 | NAND inputs tied together, acting as inverter input |
| IC6:2 | Probably Out1 |
| IC6:22 | Probably Input1 / comparator input |
| R99 | Pull-up at IC6:22 |
| R100 | Pull-down at IC6:22 |
| R101 / R43 | Part of divider / coupling network to IC6:22 / IC20 input |
| IC20:6 | Inverted output to IC1:9 |

---


## Fuses / Links
| RefDes | Known Connection / Function | Notes |
|---|---|---|
| F1 | IC9:5 to IC7 clock inputs IC7:4/7/28 | IC7 timer clock feed. |
| F2 | IC20:3 to IC10:22 / IC11:22 / IC3:19 `/OE` net | Global read `/OE` link. |
| F3 | IC20:1 / R/W net to IC1:47 | R/W link. |
| F4 | IC20:2 / E net to IC1:48 | E / bus enable link. |

---


## Current Decode Model

| Address Window | Decoder Output | Device |
|---|---|---|
| `0x2000-0x23FF` | IC17 `/Y0` | IC7 timer |
| `0x2400-0x27FF` | IC17 `/Y1` | IC4 PIA |
| `0x2800-0x2BFF` | IC17 `/Y2` | IC3 input buffer |
| `0x2C00-0x2FFF` | IC17 `/Y3` | IC10 SRAM window |
| `0x3000-0x3FFF` | IC17 `/Y4`-`/Y7` | Not wired / unused decoder space |

## Read Cycle Model

IC20 Gate 1 forms a global active-low read output-enable from `R/W` and `E`. That output is routed to SRAM `/OE`, EPROM `/OE`, and IC3 `/OE2`. Individual devices still need their own chip select.

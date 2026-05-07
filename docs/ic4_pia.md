# IC4 HD63BP21P PIA

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## IC4 HD63BP21P PIA Notes
### Bus / Select

| IC4 Pin | Signal | Connection / Notes |
|---:|---|---|
| 21 | `/R/W` | R/W net, IC20:1, IC1:47 via F3 |
| 23 | `/CS2` | IC17 `/Y1`, maps IC4 to `0x2400-0x27FF` |
| 25 | E | E / bus-enable net, IC20:2, IC1:48 via F4 |
| 26-33 | D7-D0 | Connected to data bus |
| 35 | RS1 | **A0** |
| 36 | RS0 | **A1** |
| 34 | `/RESET` | Shared reset with IC7:8 and external D7 |
| 40 | CA1 | Hard GND |
| 37, 38 | n.c. | Not connected |

### Register Addressing

Because IC4 Pin35 RS1 = A0 and Pin36 RS0 = A1, the register order is mirrored compared with the default assumption.

| Address | A1 / RS0 | A0 / RS1 | Effective RS1:RS0 | Interpreted Register |
|---|---:|---:|---:|---|
| `0x2400` | 0 | 0 | 00 | Port A / DDRA |
| `0x2401` | 0 | 1 | 10 | Port B / DDRB |
| `0x2402` | 1 | 0 | 01 | CRA |
| `0x2403` | 1 | 1 | 11 | CRB |

**Important correction:** A firmware access to `0x2401` Bit7 is therefore not CRA Bit7 / CA1. It is most likely Port B Bit7 = IC4 Pin17, which has R30 pull-up and goes to external A20. IC4 CA1 is Pin40 and is tied hard to GND.

### IC4 Observed Pin List

| IC4 Pin | Connection / Notes |
|---:|---|
| 7 | R39 pull-up + R42 pull-down |
| 8 | R38 pull-up + R41 pull-down |
| 9 | R37 pull-up + R40 pull-down |
| 10 | R36 pull-up + IC5:6 |
| 11 | R35 pull-up + IC5:10 |
| 12 | R34 pull-up + IC5:8 |
| 13 | R33 pull-up + IC5:9 |
| 14 | R32 pull-up + IC5:7 |
| 15 | R31 pull-up + IC800:7 |
| 16 | External B8 |
| 17 | R30 pull-up + external A20 |
| 18 | GND |
| 19 | R28 pull-down |
| 21 | R/W net |
| 22 | R26 pull-down |
| 23 | IC17:14 `/Y1` |
| 24 | R27 pull-down |
| 29 | R25 pull-down |
| 34 | `/RESET`, shared with IC7:8 and external D7 |
| 37, 38 | n.c. |
| 40 | CA1 hard GND |

---


## Key Correction

The important trap is `0x2401`. Because IC4 register select wiring is mirrored, `0x2401 Bit7` should be treated as Port B Bit7 / IC4 Pin17 / external A20 candidate, not as CRA Bit7 / CA1. CA1 is IC4 Pin40 and is tied hard to GND.

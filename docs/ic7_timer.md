# IC7 HD63B40P Timer

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## IC7 HD63B40P Timer Notes
### Bus / Select / Clock

| IC7 Pin | Signal | Connection / Notes |
|---:|---|---|
| 10 | RS0 | A0 |
| 11 | RS1 | A1 |
| 12 | RS2 | A2 |
| 13 | `/R/W` | R/W net, IC20:1, IC1:47 via F3 |
| 15 | `/CS0` | IC17 `/Y0`, maps IC7 to `0x2000-0x23FF` |
| 16 | CS1 | VCC, permanently enabled |
| 17 | E | E / bus-enable net, IC20:2, IC1:48 via F4 |
| 18 | GND | Ground |
| 8 | `/RESET` | IC4:34 and external D7 |
| 9 | `/IRQ` | Not connected |
| 14 | VCC | Supply |

### Gate / Clock / Output Pins

| IC7 Pin | Signal / Function | Connection / Notes |
|---:|---|---|
| 2 | `/G2` | GND |
| 5 | `/G3` | GND |
| 26 | `/G1` | GND |
| 4 | `/C2` | Connected with IC7:7 and IC7:28 via F1 to IC9:5 |
| 7 | `/C3` | Connected with IC7:4 and IC7:28 via F1 to IC9:5 |
| 28 | `/C1` | Connected with IC7:4 and IC7:7 via F1 to IC9:5 |
| 27 | O1 | External C14 with pull-up R85/R86 context; routed to IC701:4 |
| 3 | O2 | External C15 with pull-up R86/R85 context; routed to IC701:5 |
| 6 | O3 | External C16 with pull-up R87; routed to IC701:6 |

Timer outputs O1/O2/O3 are likely direct timer/pulse outputs used by the SE074 injector pre-driver logic on the secondary board.

---


## Current Functional Interpretation

- IC7 is selected by IC17 `/Y0` in the `0x2000-0x23FF` range.
- CS1 is permanently enabled by VCC.
- `/IRQ` is not connected, so firmware probably polls the timer or uses output functions without CPU interrupt service.
- Gate inputs are tied to GND.
- Clock inputs `/C1`, `/C2`, `/C3` share the divided clock from IC9 Q1.
- Outputs O1/O2/O3 route to external C14/C15/C16 and then to IC701 SE074.

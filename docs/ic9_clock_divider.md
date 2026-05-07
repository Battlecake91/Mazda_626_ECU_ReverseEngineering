# IC9 74HC74AP Clock Divider

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## IC9 74HC74AP Clock Divider
IC9 is wired as a two-stage frequency divider for the IC7 timer clock.

| IC9 Pin | Function | Connection / Notes |
|---:|---|---|
| 1 | `/1CLR` | VCC |
| 13 | `/2CLR` | VCC |
| 11 | CLK2 | E / bus-enable net: IC4:25, IC10:26, IC20:2, IC7:17, F4 -> IC1:48 |
| 12 | D2 | Connected to Pin8 `/Q2`, so FF2 toggles |
| 8 | `/Q2` | Connected to Pin12 D2 |
| 9 | Q2 | Feeds IC9 Pin3 CLK1 |
| 3 | CLK1 | Fed from Pin9 Q2 |
| 2 | D1 | Connected to Pin6 `/Q1`, so FF1 toggles |
| 6 | `/Q1` | Connected to Pin2 D1 |
| 5 | Q1 | Via F1 to IC7 Pins4/7/28 clock inputs |
| 4 | `/1PRE` | VCC |
| 10 | `/2PRE` | VCC |

Result: E / bus-enable clocks FF2; FF2 produces E/2; FF1 produces E/4 on IC9:5/F1 for IC7 clocks.

---


## Summary

IC9 takes the E / bus-enable clock on Flip-Flop 2, divides it by two, then feeds Flip-Flop 1, which divides again. IC9 Pin5 therefore supplies approximately `E/4` to the IC7 timer clock inputs through F1.

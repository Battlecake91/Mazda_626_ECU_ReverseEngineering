# IC9 74HC74 Timer Clock Divider

## Device

`IC9` is a `74HC74AP` dual D flip-flop.

It is wired as a two-stage frequency divider. Its output feeds all three clock inputs of the `IC7` HD63B40P timer.

## Flip-Flop 2: First Divider Stage

| IC9 pin | Function | Connection |
|---:|---|---|
| 11 | `CLK2` | Bus enable / `E` net: `IC4:25`, `IC10:26`, `IC20:2`, `IC7:17`, `F4 -> IC1:48`. |
| 12 | `D2` | Connected to `pin8 /Q2`. |
| 8 | `/Q2` | Fed back to `D2`. |
| 9 | `Q2` | Drives `IC9 pin3 CLK1`. |
| 10 | `/2PRE` | VCC, inactive. |
| 13 | `/2CLR` | VCC, inactive. |

Because `D2` is tied to `/Q2`, FF2 toggles on each clock edge at `CLK2`.

## Flip-Flop 1: Second Divider Stage

| IC9 pin | Function | Connection |
|---:|---|---|
| 3 | `CLK1` | Driven by `IC9 pin9 Q2`. |
| 2 | `D1` | Connected to `pin6 /Q1`. |
| 6 | `/Q1` | Fed back to `D1`. |
| 5 | `Q1` | Through `F1` to `IC7 pins 4, 7, 28`. |
| 4 | `/1PRE` | VCC, inactive. |
| 1 | `/1CLR` | VCC, inactive. |

Because `D1` is tied to `/Q1`, FF1 toggles on each clock edge from `Q2`.

## Result

The divider chain is:

```text
E / bus-enable net -> IC9 FF2 -> E/2 -> IC9 FF1 -> E/4 -> IC7 C1/C2/C3
```

`IC9 pin5 / F1` is therefore the timer clock source for all three timer channels.

## Interpretation

If `IC1` bus enable / `E` is derived from the MCU oscillator timing, then `IC7` sees a stable divided clock. With an 8 MHz crystal, the exact timer input frequency still depends on the MCU internal bus clock relationship and edge polarity. This should be confirmed with a logic analyzer on `IC9:11`, `IC9:9`, and `IC9:5`.

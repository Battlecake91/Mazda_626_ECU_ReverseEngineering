# IC1 Main Controller Pin Mapping

`IC1 = SC402617FN`

Current interpretation: main MCU or Denso custom controller with external address/data bus and Motorola-style control signals.

## Address bus pins

| IC1 pin | Signal |
|---:|---|
| 15 | `A14` |
| 16 | `A13` |
| 17 | `A12` |
| 18 | `A11` |
| 19 | `A10` |
| 20 | `A9` |
| 21 | `A8` |
| 71 | `A7` |
| 72 | `A6` |
| 73 | `A5` |
| 74 | `A4` |
| 75 | `A3` |
| 76 | `A2` |
| 77 | `A1` |
| 78 | `A0` |

## Data bus pins

| IC1 pin | Signal |
|---:|---|
| 49 | `IO0` / `D0` |
| 50 | `IO1` / `D1` |
| 51 | `IO2` / `D2` |
| 52 | `IO3` / `D3` |
| 53 | `IO4` / `D4` |
| 54 | `IO5` / `D5` |
| 55 | `IO6` / `D6` |
| 56 | `IO7` / `D7` |

## Power, ground, and clock pins

| IC1 pin | Signal / connection |
|---:|---|
| 22 | `VCC` |
| 23 | `GND` |
| 40 | `GND` |
| 41 | `VCC` |
| 44 / 45 | Crystal pins, connected to `X1 = 8 MHz` |
| 46 | `GND` |
| 66 | `VCC` |
| 83 | `VCC` |

## Bus-control related pins

| IC1 pin | Connection | Current interpretation |
|---:|---|---|
| 47 | via `F3` to `IC20:1`, `IC10:27`, `IC4:21`, `IC7:13` | `R/W`-like bus direction signal |
| 48 | via `F4` to `IC20:2`, `IC10:26`, `IC4:25`, `IC7:17`, `IC9:11` | `E` / bus-enable / system-clock-like signal |

## SE123 control-side connections

| IC1 pin | Connected to |
|---:|---|
| 25 | `IC800:4` |
| 27 | `IC800:5` |
| 28 | `IC800:6` |
| 57 | `IC800:8` |
| 58 | `IC800:9` |
| 59 | `IC800:10` |
| 60 | `IC5:4` |
| 80 | `IC5:5` |

## Notes

Pins `IC1:19` and `IC1:21` are confirmed as address lines, but they also appear in glue-logic observations through `IC20`. Keep these observations separate until the whole net is rechecked. A naming collision or tracing mix-up is possible.

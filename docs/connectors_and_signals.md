# Connectors and Signals

## Connector naming convention

External ECU connector pins are currently named:

- `A1..A20`
- `B1..B20`
- `C1..C20`
- `D1..D20`

Example: `A3` means connector block `A`, pin `3`.

## Known SE123 connector outputs

### IC5 SE123

| IC5 pin | Connector / net |
|---:|---|
| 14 | `C4` |
| 15 | `C11` |
| 16 | `C12` |
| 17 | `D16` |
| 18 | `D14` |
| 19 | `A10` |
| 20 | `B12` |
| 21 | `A1` |

### IC800 SE123

| IC800 pin | Connector / net |
|---:|---|
| 14 | `C4` |
| 15 | `D12` |
| 16 | `D19` |
| 17 | `D18` |
| 18 | `A9` |
| 19 | `C20` |
| 20 | `D13` |
| 21 | `D15` |
| 22 | `C19` |

## IC3 / IC500 / ribbon-related connections

| IC3 input | Connector / route | Source |
|---|---|---|
| `A0` | `A5` | `IC500:3` |
| `A1` | `A4` | `IC500:7` |
| `A2` | `B5` | `IC500:8` |
| `A3` | `B6` | `IC500:4` |
| `A4` | local | `IC6:8` |
| `A5` | `B7` | `IC500:6` |
| `A6` | `A6` | `IC500:10` |
| `A7` | `B9` | `IC500:11` |

`IC3` outputs `11..18` go to the data bus `D0..D7`.

## Other known connector observations

| Net | Observation |
|---|---|
| `B8` | connected to `IC4:16` |
| `C4` | shared with `IC5:14`, `IC800:14`, and `SE074:15` |
| `A1` | connected to SE123 output-side path with transistor marked `23` underlined |
| `C11` | has unpopulated resistor to transistor and goes to external connector |
| `C12` | has `4.3 kΩ`, transistor, and goes outside |

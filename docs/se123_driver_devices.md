# SE123 Driver Devices

## Part identity

Two devices are documented:

- `IC5 = SE123`
- `IC800 = SE123`

Current interpretation:

> Denso custom multi-channel output driver, likely low-side or open-collector/open-drain style.

## Common observed pin roles

| Pin | Observed role |
|---:|---|
| 1 | `VCC` |
| 11 | `GND` |
| 12 | `GND` |
| 13 | `VCC` |
| 24 | `12 V` |

## Working channel-mirror hypothesis

| Logic-side pin | Output-side pin |
|---:|---:|
| 10 | 14 |
| 9 | 15 |
| 8 | 16 |
| 7 | 17 |
| 6 | 18 |
| 5 | 19 |
| 4 | 20 |
| 3 | 21 |
| 2 | 22 |

## IC5 known output-side connections

| IC5 pin | Net / destination |
|---:|---|
| 14 | `C4` |
| 15 | `C11` |
| 16 | `C12` |
| 17 | `D16` |
| 18 | `D14` |
| 19 | `A10` |
| 20 | `B12` |
| 21 | `A1` |
| 22 | not connected |
| 23 | not connected |

Additional context:

- `C4` also goes to `SE074 pin 15`.
- `A1` has a transistor with marking `23` underlined in an SOT23-like package.
- `C11` has an unpopulated resistor to a transistor and goes to the external connector.
- `C12` has a `4.3 kΩ` resistor, a transistor, and goes outside the ECU.

These observations support the interpretation that the 12 V side of SE123 contains outputs rather than inputs.

## IC800 known output-side connections

| IC800 pin | Net / destination |
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
| 23 | not connected |

## IC1 to SE123 control-side connections

| IC1 pin | SE123 connection |
|---:|---|
| 25 | `IC800:4` |
| 27 | `IC800:5` |
| 28 | `IC800:6` |
| 57 | `IC800:8` |
| 58 | `IC800:9` |
| 59 | `IC800:10` |
| 60 | `IC5:4` |
| 80 | `IC5:5` |

## Next tests

- Determine low-side vs high-side vs open-collector behavior.
- Toggle known control pins and measure output pins with a current-limited dummy load.
- Correlate firmware writes to SE123 control lines.

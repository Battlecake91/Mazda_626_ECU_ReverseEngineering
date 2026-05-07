# SE123 Driver Devices

## Devices

Two Denso custom devices marked `SE123` are present:

| Refdes | Marking | Package | Current interpretation |
|---|---|---|---|
| `IC5` | `SE123` | DIP-24 | Multi-channel output driver / interface IC. |
| `IC800` | `SE123` | DIP-24 | Multi-channel output driver / interface IC. |

## Power Pins

| Pin | Net |
|---:|---|
| 1 | VCC |
| 11 | GND |
| 12 | GND |
| 13 | VCC |
| 24 | 12 V |

## Working Channel Mirror Hypothesis

The device may map logic-side pins `2-10` to 12 V / external-side pins `22-14` in reverse order:

| Logic-side pin | External / driver-side pin |
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

This remains a working hypothesis. The surrounding circuitry suggests output-driver behavior rather than pure input behavior.

## IC5 Known Connections

### External / 12 V Side

| IC5 pin | Connection / note |
|---:|---|
| 14 | External `C4`. |
| 15 | External `C11`. |
| 16 | External `C12`. |
| 17 | External `D16`. |
| 18 | External `D14`. |
| 19 | External `A10`. |
| 20 | External `B12`. |
| 21 | External `A1`. |
| 22 | n.c. |
| 23 | n.c. |

### Logic / Control Side

Known connections include:

| IC5 pin | Source / related signal |
|---:|---|
| 4 | `IC1:60`. |
| 5 | `IC1:80`. |
| 6 | `IC4:10`, with `R36` pull-up. |
| 7 | `IC4:14`, with `R32` pull-up. |
| 8 | `IC4:12`, with `R34` pull-up. |
| 9 | `IC4:13`, with `R33` pull-up. |
| 10 | `IC4:11`, with `R35` pull-up. |

## IC800 Known Connections

### External / 12 V Side

| IC800 pin | Connection / note |
|---:|---|
| 14 | External `C4`. |
| 15 | External `D12`. |
| 16 | External `D19`. |
| 17 | External `D18`. |
| 18 | External `A9`. |
| 19 | External `C20`. |
| 20 | External `D13`. |
| 21 | External `D15`. |
| 22 | External `C19`. |
| 23 | n.c. |

### Logic / Control Side

| IC800 pin | Source / related signal |
|---:|---|
| 4 | `IC1:25`. |
| 5 | `IC1:27`. |
| 6 | `IC1:28`. |
| 7 | `IC4:15`, with `R31` pull-up. |
| 8 | `IC1:57`. |
| 9 | `IC1:58`. |
| 10 | `IC1:59`. |

## Surrounding Circuit Clues

- `C4` also goes to `SE074 pin15` context.
- `A1` has a transistor marked `23` with underline in a SOT23-like package.
- `C11` has an unpopulated resistor path to a transistor and goes to the external ECU connector.
- `C12` has a 4.3 k resistor, a transistor, and goes outside.

These observations support the idea that the 12 V side pins are outputs or driver-side nodes rather than simple digital inputs.

## Current Interpretation

`SE123` is likely a Denso multi-channel low-side / open-collector-like driver or protected output interface. It should be documented as a custom driver block until channel behavior is verified with powered bench measurements.

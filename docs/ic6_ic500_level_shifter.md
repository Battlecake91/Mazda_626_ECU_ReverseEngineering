# IC6 / IC500 Denso Comparator and Level Shifter

## Part identity

Observed markings / references:

- `IC6 = 151821-0020`
- `IC500 = 151821-0020` on the ribbon-connected board
- Also referenced as `D151821-0020`

Current interpretation:

> Denso custom comparator / 12 V-to-5 V level-shifter IC, DIP-24.

This is **not** a simple ten-channel linear 12 V-to-5 V converter. It appears to combine comparator behavior, ground-detect inputs, and +12 V-detect inputs.

## Power pins

| Pin | Function |
|---:|---|
| 1 | `Vin +12 V` |
| 12 | `GND` |
| 24 | `Vin +5 V` |

## Output side

Pins `2..11` are 5 V logic outputs.

| Pin | Function |
|---:|---|
| 2 | `Out1` |
| 3 | `Out2` |
| 4 | `Out3` |
| 5 | `Out4` |
| 6 | `Out5` |
| 7 | `Out6` |
| 8 | `Out7` |
| 9 | `Out8` |
| 10 | `Out9` |
| 11 | `Out10` |

## Input / comparator side

Pins `13..23` are input/comparator-side pins in reverse order.

| Pin | Function |
|---:|---|
| 23 | `Input0`, comparator reference/input |
| 22 | `Input1`, comparator input |
| 21 | `Input2` |
| 20 | `Input3` |
| 19 | `Input4` |
| 18 | `Input5` |
| 17 | `Input6` |
| 16 | `Input7` |
| 15 | `Input8` |
| 14 | `Input9` |
| 13 | `Input10` |

## Behavioral notes

### Comparator channel

`Out1` on pin 2 is the comparator output for `Input1` pin 22 against `Input0` pin 23.

| Condition | Pin 2 behavior |
|---|---|
| `V(pin22) < V(pin23)` | low / pulled to ground |
| `V(pin22) > V(pin23)` | open / high impedance |

This suggests an open-collector or open-drain-style output.

### Ground-detect channels

Outputs `Out2..Out8` on pins `3..9` correspond to inputs `Input2..Input8` on pins `21..15`.

| Input state | Output behavior |
|---|---|
| input floating | output high, approximately +5 V |
| input grounded | output low, approximately 0 V |

### +12 V-detect channels

Outputs `Out9..Out10` on pins `10..11` correspond to inputs `Input9..Input10` on pins `14..13`.

| Input state | Output behavior |
|---|---|
| input floating | output low, approximately 0 V |
| input driven with +12 V | output high, approximately +5 V |

## Known IC6 connections

| Source | Connection |
|---|---|
| `IC4:4` | through `1 kΩ` to `IC6:6` |
| `IC4:5` | through `1 kΩ` to `IC6:5` |
| `IC4:6` | through `1 kΩ` to `IC6:3` |
| `IC6:22` | through `R101` / pull network to `IC20:4/5` inverter input |

`IC6:22` is part of the comparator channel, so this may be a thresholded diagnostic, ignition, power, or analog state input.

## IC500 / IC3 relation

`IC500` is on the other board and feeds the 74HC541 input buffer via the ribbon connector.

| IC3 input | Connector | IC500 pin |
|---|---|---:|
| `IC3 A0` | `A5` | `IC500:3` |
| `IC3 A1` | `A4` | `IC500:7` |
| `IC3 A2` | `B5` | `IC500:8` |
| `IC3 A3` | `B6` | `IC500:4` |
| `IC3 A4` | local | `IC6:8` |
| `IC3 A5` | `B7` | `IC500:6` |
| `IC3 A6` | `A6` | `IC500:10` |
| `IC3 A7` | `B9` | `IC500:11` |

This strongly suggests that `IC3` presents conditioned external switch or diagnostic states to the CPU data bus when selected.

## Open questions

- Confirm output type for pins `2..11`: open collector/open drain, push-pull, or mixed.
- Trace each `IC6` and `IC500` channel to ECU connector pins.
- Confirm the real-world function of `IC6:22`.
- Correlate firmware reads through `IC3` with the physical input states.

# IC6 / IC500 D151821-0020 Level Shifter / Comparator

## Device

`IC6` and `IC500` are marked `151821-0020` / `D151821-0020`. Current interpretation is that this is a Denso custom mixed comparator / level-shifter IC, not a simple 10-channel 12 V to 5 V translator.

The part appears to interface external automotive-level signals to 5 V logic.

## Proposed Pinout

| Pin | Function | Notes |
|---:|---|---|
| 1 | `Vin +12 V` | 12 V supply / input supply. |
| 2 | `Out1` | 5 V logic output. Comparator output for pin22 vs pin23 behavior. |
| 3 | `Out2` | 5 V logic output. |
| 4 | `Out3` | 5 V logic output. |
| 5 | `Out4` | 5 V logic output. |
| 6 | `Out5` | 5 V logic output. |
| 7 | `Out6` | 5 V logic output. |
| 8 | `Out7` | 5 V logic output. |
| 9 | `Out8` | 5 V logic output. |
| 10 | `Out9` | 5 V logic output. |
| 11 | `Out10` | 5 V logic output. |
| 12 | GND | Ground. |
| 13 | `Input10` | 12 V-detect style input. |
| 14 | `Input9` | 12 V-detect style input. |
| 15 | `Input8` | Ground-detect style input. |
| 16 | `Input7` | Ground-detect style input. |
| 17 | `Input6` | Ground-detect style input. |
| 18 | `Input5` | Ground-detect style input. |
| 19 | `Input4` | Ground-detect style input. |
| 20 | `Input3` | Ground-detect style input. |
| 21 | `Input2` | Ground-detect style input. |
| 22 | `Input1` | Comparator input relative to pin23. |
| 23 | `Input0 / reference` | Comparator reference / input. |
| 24 | `Vin +5 V` | 5 V supply. |

## Behavioral Notes From Source / Tests

### Output 1 / Comparator Behavior

`Out1` at pin 2 acts as comparator output for pin 22 against pin 23:

| Condition | Pin 2 behavior |
|---|---|
| `Pin22 < Pin23` | Pin 2 pulls low / toward GND. |
| `Pin22 > Pin23` | Pin 2 open / high-impedance. |

This implies an open-collector / open-drain style output needing pull-up or external logic interpretation.

### Outputs 2-8: Ground Detect Behavior

For outputs 2-8 (`pins 3-9`) and corresponding inputs (`pins 21-15`):

| Input state | Output state |
|---|---|
| Input floating | `+5 V` logic high. |
| Input grounded | `0 V` logic low. |

### Outputs 9-10: 12 V Detect Behavior

For outputs 9-10 (`pins 10-11`) and corresponding inputs (`pins 14-13`):

| Input state | Output state |
|---|---|
| Input floating | `0 V` logic low. |
| Input driven to `+12 V` | `+5 V` logic high. |

## IC3 Input Port Relationship

Most `IC3` input-buffer pins are fed from `IC500` outputs:

| IC3 input | Source |
|---|---|
| `A0` | Connector `A5` -> `IC500:3` |
| `A1` | Connector `A4` -> `IC500:7` |
| `A2` | Connector `B5` -> `IC500:8` |
| `A3` | Connector `B6` -> `IC500:4` |
| `A4` | `IC6:8` |
| `A5` | Connector `B7` -> `IC500:6` |
| `A6` | Connector `A6` -> `IC500:10` |
| `A7` | Connector `B9` -> `IC500:11` |

## Important Interpretation

The D151821-0020 should not be modeled as a uniform translator. It contains mixed behavior:

- one comparator channel,
- multiple ground-detect channels,
- multiple 12 V-detect channels.

Firmware interpretation of the `IC3` input byte must account for these different polarities.

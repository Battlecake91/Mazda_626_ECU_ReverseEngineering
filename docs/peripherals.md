# Peripherals

## IC4: HD63BP21P

`IC4 = HD63BP21P`, likely a Motorola-compatible PIA-style parallel I/O peripheral.

### Known pin connections

| IC4 pin | Connection / notes |
|---:|---|
| 1 | `VSS` |
| 2 | through `1 kΩ` to `D2` |
| 3 | through `1 kΩ` to `IC21:7` |
| 4 | through `1 kΩ` to `IC6:6` |
| 5 | through `1 kΩ` to `IC6:5` |
| 6 | through `1 kΩ` to `IC6:3` |
| 7 | `R39` pull-up to `VCC`, `R42` pull-down not populated |
| 8 | `R38` pull-up to `VCC`, `R41` pull-down not populated |
| 9 | `R37` pull-up not populated, `R40` pull-down |
| 10 | `R36` pull-up to `VCC` |
| 11 | `R35` pull-up to `VCC` |
| 12 | `R34` pull-up to `VCC` |
| 13 | `R33` pull-up to `VCC` |
| 14 | `R32` pull-up to `VCC` |
| 15 | `R31` pull-up to `VCC` |
| 16 | external connector `B8` |
| 18 | connected to pin 19 through `R28` |
| 19 | connected to pin 18 through `R28` |
| 20 | `VCC` |
| 21 | `IC10:27`, `IC7:13`, `IC20:1`; likely `R/W` |
| 22 | `R26` pull-up to `VCC` |
| 23 | `IC17:14`, likely chip-select |
| 24 | `R27` pull-up to `VCC` |
| 25 | `IC7:17`, `IC9:11`, `IC10:26`, `IC20:2`, via `F4` to `IC1:48`; likely `E` |
| 26 | `IC7:18`, `IC11:19`, `IC10:19`, `IC3:18` |

### Firmware note

`0x2401, bit 7` has been identified as interesting and likely PIA/peripheral-related.

Suggested temporary Ghidra label:

```text
PIA_2401_BIT7_TODO
```

## IC7: HD63B40P

`IC7 = HD63B40P`, likely a Motorola-style timer / peripheral device.

Known pins:

| IC7 pin | Connection / interpretation |
|---:|---|
| 13 | `IC10:27`, `IC4:21`, `IC20:1`; likely `R/W` |
| 17 | `IC10:26`, `IC4:25`, `IC9:11`, `IC20:2`, via `F4` to `IC1:48`; likely `E` / system clock |
| 18 | `IC4:26`, `IC11:19`, `IC10:19`, `IC3:18` |

## IC13: TC4051BP

Likely analog sensor multiplexing or diagnostic routing. Exact channel mapping is not yet documented.

## IC21: TLC272

Dual op amp. `IC21:7` connects through `1 kΩ` to `IC4:3`, implying a firmware-visible analog/comparator-conditioned signal.

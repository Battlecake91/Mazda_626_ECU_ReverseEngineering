# Chip-Select and Glue Logic

## IC17: 74HC138 address decoder

`IC17 = 74HC138AP`

Known connections:

| IC17 pin | Function | Connection |
|---:|---|---|
| 1 | `A0` | address `A10` |
| 2 | `A1` | address `A11` |
| 3 | `A2` | address `A12`, also `IC10:2`, `IC1:17` |
| 4 | `/G2A` | connected to `IC20:12/13` in corrected notes |
| 5 | `/G2B` | address `A14` |
| 6 | `G1` | address `A13` |
| 10 | `/Y5` | connected to `IC10:20`, likely RAM select |
| 13 | `/Y2` | connected to `IC3:1`, input-buffer select |
| 14 | `/Y1` | connected to `IC4:23`, PIA select |

## IC20: 74HC00 glue logic

`IC20 = 74HC00AP`

### Corrected pin map

| IC20 pin | Connection / meaning |
|---:|---|
| 1 | `R/W`-like signal: `IC10:27`, `IC4:21`, `IC7:13`, via `F3` to `IC1:47` |
| 2 | `E` / bus-enable-like signal: `IC10:26`, `IC4:25`, `IC7:17`, `IC9:11`, via `F4` to `IC1:48` |
| 3 | global active-low read `/OE`; via `F2` to `IC10:22`, `IC11:22`, `IC3:19` |
| 4 | tied to pin 5; input-conditioning logic through `R101` to `IC6:22` |
| 5 | tied to pin 4 |
| 6 | output of gate 2; connected to `IC1:19` in current notes, needs recheck |
| 8 | through `R606` to `IC1:21` |
| 9 | tied to pin 10 and `C10` |
| 10 | tied to pin 9 and `C10` |
| 11 | output of NAND gate 4; connected to `IC11:20` |
| 12 | tied to pin 13 and connected to `IC17:4` |
| 13 | tied to pin 12 and connected to `IC17:4` |
| 14 | `VCC`; also related to `R56` in the current trace notes |

### Gate 1: global read `/OE`

Inputs:

- `IC20:1` = `R/W`-like signal,
- `IC20:2` = `E` / bus-enable-like signal.

Output:

- `IC20:3` = shared active-low read `/OE`.

Connected devices:

- `IC10:22` SRAM `/OE`,
- `IC11:22` ROM `/OE`,
- `IC3:19` 74HC541 `/OE2`.

This strongly suggests that ROM, RAM, and the input buffer only drive the data bus during valid read cycles.

### Gate 2: IC6 comparator path

Pins `IC20:4` and `IC20:5` are tied together and connected through `R101` to `IC6:22`, with `R99` pull-up and `R100` pull-down nearby.

The gate acts as an inverter / thresholded digital stage for the comparator-related `IC6:22` signal.

### Important correction: IC20 gate 4

A previous assumption treated `IC20:12` as an output. That is wrong for a 74HC00 package.

Correct:

- `IC20:12` and `IC20:13` are inputs.
- `IC20:11` is the output.

Current trace:

- `IC20:12/13` go to `IC17:4`.
- `IC20:11` goes to `IC11:20`.

This correction is critical for ROM chip-enable reconstruction. Tiny NAND gates: small enough to hide, annoying enough to ruin a memory map.

## IC3: 74HC541 input-buffer enable

| IC3 pin | Function | Connection |
|---:|---|---|
| 1 | `/OE1` | `IC17:13` |
| 19 | `/OE2` | global read `/OE` from `IC20:3` |
| 11..18 | outputs | data bus `D0..D7` |

Interpretation:

`IC3` is selected by address decode and additionally gated by read-cycle `/OE`.

## Open checks

- Re-probe `IC20:12/13`, `IC20:11`, `IC17:4`, `R56`, and any `IC1:14` relation.
- Confirm `IC20:3` timing against `R/W` and `E` using a logic analyzer.
- Resolve the apparent conflict around `IC20:6 -> IC1:19` while `IC1:19` is also `A10`.

# Peripheral Devices

## IC4: HD63BP21P

`IC4` is an `HD63BP21P`, a PIA-style parallel interface device.

## IC4 pin mapping

| IC4 pin | Connection / note |
|---:|---|
| 1 | Vss |
| 2 | Through 1k to D2 |
| 3 | Through 1k to IC21 pin 7 |
| 4 | Through 1k to IC6 pin 6 |
| 5 | Through 1k to IC6 pin 5 |
| 6 | Through 1k to IC6 pin 3 |
| 7 | R39 pull-up to VCC and R42 pull-down, not populated |
| 8 | R38 pull-up to VCC and R41 pull-down, not populated |
| 9 | R37 pull-up, not populated, and R40 pull-down |
| 10 | R36 pull-up to VCC |
| 11 | R35 pull-up to VCC |
| 12 | R34 pull-up to VCC |
| 13 | R33 pull-up to VCC |
| 14 | R32 pull-up to VCC |
| 15 | R31 pull-up to VCC |
| 16 | B8 connector pin |
| 18 | R28 to pin 19 |
| 19 | R28 to pin 18 |
| 20 | VCC |
| 21 | IC10 pin 27 and IC7 pin 13, R/W net |
| 22 | R26 pull-up to VCC |
| 23 | IC17 pin 14 |
| 24 | R27 pull-up to VCC |
| 25 | IC7 pin 17, IC10 pin 26, IC20 pin 2, F4 to IC1 pin 48 |
| 26 | IC7 pin 18, IC11 pin 19, IC10 pin 19, IC3 pin 18 |

## HD63BP21P functional notes

Known datasheet-relevant notes:

- `PB0..PB7` are on pins `10..17`.
- `CB1` and `CB2` are interrupt/peripheral control pins.
- `CA1` and `CB1` are input-only interrupt flag inputs.
- `CB2` can be programmed as interrupt input or peripheral control output.
- When configured as input, `CB2` is high impedance.

## IC7: HD63B40P

Known relevant pins:

| IC7 pin | Connection / note |
|---:|---|
| 13 | R/W net with IC10 pin 27, IC4 pin 21 and IC20 pin 1 |
| 17 | `E` / system clock net with IC4 pin 25, IC10 pin 26 and IC20 pin 2 |
| 18 | Connected to IC4 pin 26, IC11 pin 19, IC10 pin 19 and IC3 pin 18 context |

Current interpretation:

- `IC7` is bus-connected.
- `IC7 pin 17` being `E` strongly supports a Motorola-style external bus timing model.
- Exact register address decoding is not yet known.

## IC3: 74HC541 input buffer

`IC3` has its outputs on the data bus and is likely a memory-mapped input port.

### Data bus side

| IC3 pin | Connection |
|---:|---|
| 11..18 | Data bus `D0..D7` / `IO0..IO7` |
| 19 | `/OE2`, connected to global `/OE` line through F2 / IC20 pin 3 |
| 1 | `/OE1`, connected to IC17 pin 13 |

This means `IC3` is likely enabled only when:

1. Its address decode output from `IC17 pin 13` is active.
2. The global read-enable `/OE` is active.

### Input side

| IC3 input | Route |
|---|---|
| A0 | Connector A5 -> IC500 pin 3 |
| A1 | Connector A4 -> IC500 pin 7 |
| A2 | Connector B5 -> IC500 pin 8 |
| A3 | Connector B6 -> IC500 pin 4 |
| A4 | IC6 pin 8 |
| A5 | Connector B7 -> IC500 pin 6 |
| A6 | Connector A6 -> IC500 pin 10 |
| A7 | Connector B9 -> IC500 pin 11 |

## IC8: 74HC595

Known only as present. Likely used for output expansion or serially controlled latch outputs. Needs pin tracing.

## IC9: 74HC74AP

Known only as present. One known shared net:

- `IC9 pin 11` connects to the `E`/bus-enable net together with `IC7 pin 17`, `IC10 pin 26`, `IC4 pin 25`, `IC20 pin 2` and `F4` to `IC1 pin 48`.

## IC13: TC4051BP

Analog multiplexer. Needs tracing to analog input stages and MCU/PIA control pins.

## IC21: TLC272

Dual op amp. Known connection:

- `IC21 pin 7` connects through 1k to `IC4 pin 3`.

Likely part of analog conditioning or comparator-like signal processing.

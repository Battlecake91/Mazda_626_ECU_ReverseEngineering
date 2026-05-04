# Chip Select and Glue Logic

## Key devices

| Reference | Type | Function |
|---|---|---|
| IC17 | 74HC138AP | 3-to-8 decoder for active-low chip selects |
| IC20 | 74HC00AP | Quad NAND gate for bus control and signal conditioning |
| F2/F3/F4 | Unknown series elements | Currently treated as links/filters/fuses in control nets |

## Global bus control nets

### R/W-like net

This net connects:

- `IC1 pin 47` through `F3`
- `IC20 pin 1`
- `IC10 pin 27` (`R/W`)
- `IC4 pin 21`
- `IC7 pin 13`

Current interpretation: CPU read/write control.

### E / bus-enable / system-clock-like net

This net connects:

- `IC1 pin 48` through `F4`
- `IC20 pin 2`
- `IC10 pin 26` (`CE2`)
- `IC4 pin 25`
- `IC7 pin 17` (`E`, system clock)

Current interpretation: `E` or bus-cycle enable clock.

### Global `/OE` read-enable net

`IC20 gate 1` uses the `R/W` and `E`-like signals to generate a global active-low output-enable signal.

Known connections:

| IC20 pin | Connection |
|---:|---|
| 1 | `R/W` net: IC10 pin 27, IC4 pin 21, IC7 pin 13, through F3 to IC1 pin 47 |
| 2 | `E` net: IC7 pin 17, IC4 pin 25, IC10 pin 26, through F4 to IC1 pin 48 |
| 3 | Through F2 to IC10 pin 22 `/OE`, IC11 pin 22 `/OE`, IC3 pin 19 `/OE2` |

Working interpretation:

```text
IC20 gate 1:
  input A = R/W
  input B = E / bus enable
  output  = global /OE
```

This likely enables ROM, RAM and IC3 outputs during read cycles only.

## IC20 full known pin notes

| IC20 pin | Known connection | Interpretation |
|---:|---|---|
| 1 | IC10:27, IC4:21, IC7:13, F3 to IC1:47 | R/W input to NAND gate 1 |
| 2 | IC10:26, IC4:25, IC7:17, F4 to IC1:48 | E / bus-enable input to NAND gate 1 |
| 3 | F2 to IC10:22, IC11:22, IC3:19 | Global `/OE` output |
| 4 | Tied to pin 5, through circuit 1 to IC6:22 | NAND gate 2 input, used as inverter input |
| 5 | Tied to pin 4, through circuit 1 to IC6:22 | NAND gate 2 input, used as inverter input |
| 6 | IC1:19 | NAND gate 2 output |
| 8 | Through R606 to IC1:21 | Part of another gate, not fully resolved |
| 9 | Tied to pin 10 and C10 | Unknown timing/filter node |
| 10 | Tied to pin 9 and C10 | Unknown timing/filter node |
| 11 | IC11:20 | NAND gate 4 output to EPROM `/CE`/`/PGM` context |
| 12 | Tied to pin 13 and IC17:4 | NAND gate 4 input |
| 13 | Tied to pin 12 and IC17:4 | NAND gate 4 input |

## Corrected IC20 gate 4 interpretation

Earlier assumptions treated `IC20 pin 12` as an output toward `IC17 pin 4`. That is incorrect.

Correct interpretation:

- `IC20 pins 12 and 13` are inputs.
- They are tied together and connected to `IC17 pin 4`.
- `IC20 pin 11` is the output.
- `IC20 pin 11` drives `IC11 pin 20`, the EPROM `/CE` or `/PGM` control context.

In other words, gate 4 behaves as an inverter for the signal coming from `IC17 pin 4`, feeding the EPROM enable path.

## Circuit 1 near IC20 pins 4/5 and IC6 pin 22

Known circuit:

```text
IC20 pins 4 and 5 tied together
    -> R101 43k
    -> IC6 pin 22
       + R99 5.1k pull-up
       + R100 5.1k pull-down
```

Working interpretation:

- `IC20 gate 2` is wired as an inverter.
- It conditions or digitizes a signal from `IC6 pin 22`.
- The inverted output at `IC20 pin 6` goes to `IC1 pin 19`.
- Since `IC1 pin 19` is also known as `Adr10`, this must be checked carefully. It may indicate a dual-use pin, a mislabel, or a context-dependent signal.

## IC17 74HC138 notes

Known connections:

| IC17 pin | Connection |
|---:|---|
| 3 | IC10 pin 2 and IC1 pin 17 context |
| 4 | IC20 pins 12/13 |
| 10 | IC10 pin 20 |
| 13 | IC3 pin 1 `/OE1` |
| 14 | IC4 pin 23 |

Working assumptions:

- `IC17` generates active-low chip select or enable lines.
- `IC17 pin 13` selects/enables `IC3 74HC541`.
- `IC17 pin 14` selects/enables `IC4 HD63BP21P`.
- `IC17 pin 10` participates in SRAM selection.
- `IC17 pin 4` is tied into EPROM enable logic through IC20 gate 4.

## Immediate next checks

1. Trace all `IC17` inputs and enable pins.
2. Confirm all `IC17` output destinations.
3. Capture `R/W`, `E`, `/OE`, ROM `/CE`, RAM select and `IC17` outputs with a logic analyzer.
4. Correlate chip select states with firmware address accesses.

# Firmware Analysis Notes

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Current Firmware Context

The firmware is analyzed as HC11-style code with ROM placed at `0x8000`. External peripherals are memory-mapped through the IC17 decoder and must be marked volatile in Ghidra.

## Known Address Use

| Address / Symbol | Notes |
|---|---|
| `0x2000-0x23FF` | IC7 timer registers. |
| `0x2400-0x27FF` | IC4 PIA registers. |
| `0x2800-0x2BFF` | IC3 input port. |
| `0x2C00-0x2FFF` | External SRAM window. |
| `0x8000-0xFFFF` | EPROM ROM window. |

## Known Labels / Variables

| Label | Current Meaning |
|---|---|
| `DAT_0049` | Flag/status byte used with `BRCLR`. |
| `MEAS_ENABLE_FLAGS_67` | Measurement-enable flag byte; bit `0x10` observed. |
| `DAT_0192` | RAM variable, likely control/measurement related. |
| `DAT_019B` | RAM variable, likely branch/state related. |
| `DAT_8249` | ROM constant/table value `0x1F`. |

## Example Disassembly Context

```asm
bf6f 13 49 01 05    BRCLR DAT_0049,0x01,LAB_bf78
bf73 b6 01 9b       LDAA  DAT_019b
bf76 26 12          BNE   LAB_bf8a
bf78 b6 01 92       LDAA  DAT_0192
bf7b 12 67 10 03    BRSET MEAS_ENABLE_FLAGS_67,0x10,LAB_bf82
bf7f b6 82 49       LDAA  DAT_8249
```

## Analysis Rules

- Treat hardware register reads as volatile.
- Do not collapse IC4 register order to the datasheet default; this board swaps the practical address interpretation through RS wiring.
- Keep ROM constants separate from RAM variables.
- Use hardware labels aggressively for decoded peripheral addresses, because they reduce nonsense in decompiled output faster than randomly naming every function `do_stuff_final`.

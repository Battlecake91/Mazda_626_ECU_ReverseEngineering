# Ghidra HC11 Setup and Firmware Analysis Workflow

## Purpose

This file documents the current Ghidra setup used for analyzing the KLDE / KL05 Denso ECU firmware ROM.

The firmware is stored in `IC11 = 27C256`, so the raw image size is expected to be 32 KiB.

## Processor / language module

Current analysis uses a third-party Motorola HC11 / 68HC11 processor module for Ghidra.

Documented as used:

```text
Ghidra processor/language module for Motorola MC68HC11 / 68HC11
```

Exact library / repository name:

```text
TODO: add exact GitHub repository or release name used locally
```

Reason for using an HC11-style module:

- the external bus is Motorola-like,
- `R/W` and `E`-like signals are visible in the hardware,
- `HD63BP21P` and `HD63B40P` are Motorola-bus-style parts,
- Ghidra disassembly produces plausible HC11-like instructions such as `LDAA`, `BNE`, `BRCLR`, and `BRSET`.

Important caveat:

`IC1 = SC402617FN` is not proven to be a standard MC68HC11. Treat HC11 analysis as the current working model, not final silicon identification.

## Import settings

| Setting | Value / note |
|---|---|
| File type | Raw binary |
| CPU language | Motorola 68HC11 / MC68HC11 from third-party module |
| ROM size | 32 KiB |
| Base address | Not final; derive from reset vector and chip-select decoding |
| Analysis style | Conservative auto-analysis plus manual labels |

## Base-address warning

Do **not** blindly assume the ROM starts at `0x4000`.

That address was discussed earlier but questioned. The correct mapping must be derived from:

1. reset-vector location,
2. 27C256 address-line wiring,
3. `IC17` chip-select decode,
4. valid Ghidra code flow,
5. memory references matching RAM and I/O regions.

## Function creation workflow

During early analysis:

- Label obvious targets first.
- Create functions with `F` only where the entry point is credible.
- Good function candidates are `JSR` / `BSR` targets and reset/init flow targets.
- For ambiguous branch targets, label first and turn into a function later.

Practical rule:

```text
Create functions for clear call targets.
Label uncertain branch targets as LAB_xxxx or TODO_xxxx first.
```

This avoids turning data tables into fake functions, which Ghidra will happily do because apparently tools also enjoy practical jokes.

## Current labels / observed variables

| Address / label | Current meaning |
|---|---|
| `MEAS_ENABLE_FLAGS_67` | Measurement-enable or mode flag byte; bit `0x10` observed in branch logic |
| `DAT_0049` | Bit 0 tested by `BRCLR` in current snippet |
| `DAT_0192` | RAM variable loaded in branch path |
| `DAT_019B` | RAM variable loaded and tested with `BNE` |
| `DAT_8249` | ROM constant/value observed as `0x1F` in snippet |
| `0x2401 bit 7` | Interesting PIA/peripheral-related bit under investigation |

## Example observed snippet

```asm
bf6f 13 49 01 05    BRCLR  DAT_0049,0x1,LAB_bf78
bf73 b6 01 9b       LDAA   DAT_019b
bf76 26 12          BNE    LAB_bf8a

LAB_bf78:
bf78 b6 01 92       LDAA   DAT_0192
bf7b 12 67 10 03    BRSET  MEAS_ENABLE_FLAGS_67,0x10,LAB_bf82
bf7f b6 82 49       LDAA   DAT_8249 ; = 0x1F
```

Interpretation:

- `DAT_0049 bit 0` gates one branch path.
- `DAT_019B` is tested for non-zero.
- `MEAS_ENABLE_FLAGS_67 bit 4` selects a branch around a ROM constant load.
- `DAT_8249 = 0x1F` is likely a ROM constant or calibration value, depending on final memory map.

## Memory block setup recommendations

Once ranges are proven, configure memory blocks like this:

| Region type | Ghidra attributes |
|---|---|
| ROM | read + execute, not write, not volatile |
| RAM | read + write; usually not marked hardware-volatile, but runtime-changing |
| PIA / PTM registers | read + write + volatile |
| Input-buffer address range | read + volatile |
| Output register / latch range | write + volatile, read only if readback exists |

## Next Ghidra tasks

1. Confirm ROM load address using reset vectors.
2. Label all known memory-mapped peripheral candidates.
3. Mark hardware register ranges as volatile.
4. Track all accesses to `0x2401`, especially bit 7.
5. Track writes that likely drive `IC5` / `IC800` SE123 outputs.
6. Build a cross-reference table from firmware register/bit to board net.

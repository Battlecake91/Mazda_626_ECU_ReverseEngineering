# Firmware Analysis Notes

## Current analysis model

The firmware is analyzed as HC11-like Motorola 8-bit code in Ghidra using a third-party HC11 / 68HC11 language module.

This is a useful working model, not yet final proof of the exact `IC1` CPU core.

## ROM

| Item | Value |
|---|---|
| Physical ROM | `IC11 = 27C256` |
| Size | 32 KiB |
| Data width | 8 bit |
| Address pins | `A0..A14` |

## Known code/variable observations

```asm
BRCLR  DAT_0049,0x1,LAB_bf78
LDAA   DAT_019b
BNE    LAB_bf8a

LAB_bf78:
LDAA   DAT_0192
BRSET  MEAS_ENABLE_FLAGS_67,0x10,LAB_bf82
LDAA   DAT_8249 ; = 0x1F
```

| Symbol / address | Meaning / hypothesis | Confidence |
|---|---|---:|
| `DAT_0049 bit 0` | Conditional flag controlling branch path | Medium |
| `DAT_019B` | RAM variable checked for non-zero | Medium |
| `DAT_0192` | RAM variable used in fallback/default path | Medium |
| `MEAS_ENABLE_FLAGS_67 bit 0x10` | Measurement/mode-enable flag | Medium |
| `DAT_8249 = 0x1F` | ROM constant or calibration byte | Medium |
| `0x2401 bit 7` | Interesting PIA/peripheral bit under investigation | Medium |

## Function creation policy

1. Use labels freely.
2. Create functions only at credible entry points.
3. Prefer semantic labels once behavior is known.
4. Avoid over-typing data tables as code.

Recommended naming style:

```text
SUB_xxxx                  ; unknown but callable subroutine
INIT_xxx                  ; initialization routine
READ_xxx                  ; read routine
UPDATE_xxx                ; periodic update routine
PIA_xxxx_BITn_TODO        ; hardware bit under investigation
DAT_xxxx                  ; unknown RAM/ROM data
CAL_xxxx                  ; likely calibration data
```

## Hardware correlation workflow

For every interesting firmware access:

1. Record address and bit mask.
2. Determine whether the address is ROM, RAM, or I/O.
3. Check cross-references in Ghidra.
4. Map to decoded chip-select if possible.
5. Probe corresponding physical pin or connector net.
6. Rename symbol only after behavior is plausible.

## High-value targets

| Target | Why it matters |
|---|---|
| Reset vector and startup routine | Determines ROM base and CPU execution model |
| `0x2401 bit 7` | Likely PIA-related hardware behavior |
| Writes to SE123 control pins | Output-driver mapping to actuators |
| Reads through `IC3` | External switch / diagnostic input mapping |
| `IC6:22` comparator path | May be power, ignition, diagnostic, or threshold input |
| RAM variables around `0x0049`, `0x0067`, `0x0192`, `0x019B` | Control flags and runtime state |

## Logic analyzer idea

A DSLogic Plus with `sigrok-cli` can be used to watch the external bus or selected chip-select/control lines.

Useful signals:

- `E` / bus-enable line,
- `R/W`,
- global `/OE`,
- `IC17` outputs,
- selected address lines,
- selected data-bus bits,
- SE123 control pins.

Start small: `E`, `R/W`, one chip-select, and a few address/data lines. Capturing the whole universe at once is how humans invent new kinds of disappointment.

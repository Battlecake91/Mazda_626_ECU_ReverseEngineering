# Ghidra HC11 Firmware Setup

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Processor / Language Module

The firmware analysis is being done in Ghidra using a third-party Motorola 68HC11 / MC68HC11 processor-language module.

The exact library/repository name still needs to be recorded in the repo once confirmed from the local Ghidra installation or downloaded ZIP. Document it here once known instead of guessing, because fake precision is how bad schematics are born.

## Suggested Import Settings

| Setting | Value / Recommendation |
|---|---|
| CPU family | Motorola 68HC11 / HC11-compatible language module |
| ROM binary base | `0x8000` |
| ROM block size | 32 KiB for `27C256` |
| Reset vector check | EPROM dump offset `0x7FFE/0x7FFF` should correspond to CPU vector address `0xFFFE/0xFFFF` |
| Endianness | Big-endian for HC11-style vectors/opcodes |
| External peripherals | Mark as volatile, non-executable |
| External RAM | Mark read/write, non-executable, non-volatile |
| ROM | Mark read/execute, not writeable |

## Recommended Memory Blocks

| Block | Start | End | Access |
|---|---:|---:|---|
| `MCU_INTERNAL_REGS` | `0x0000` | `0x007F` | R/W, volatile |
| `MCU_INTERNAL_RAM` | `0x0080` | `0x047F` | R/W |
| `MCU_INTERNAL_EEPROM` | `0x0D80` | `0x0FFF` | R/W |
| `IC7_TIMER_BASE` | `0x2000` | `0x23FF` | R/W, volatile |
| `IC4_PIA_BASE` | `0x2400` | `0x27FF` | R/W, volatile |
| `IC3_INPUT_PORT` | `0x2800` | `0x2BFF` | R, volatile |
| `RAM_EXT` | `0x2C00` | `0x2FFF` | R/W |
| `UNUSED_DECODER_SPACE` | `0x3000` | `0x3FFF` | unmapped / placeholder only |
| `ROM_EXT` | `0x8000` | `0xFFFF` | R/X |

## Labeling Practice

- Rename meaningful functions, but do not over-label everything too early.
- Keep generated `SUB_xxxx` names until call context or behavior is reasonably clear.
- For hardware register accesses, create labels before function names become too speculative.
- `0x2401 Bit7` should be annotated as IC4 Port B Bit7 / IC4 Pin17 / external A20 candidate, not CRA/CA1.

## Known Firmware Symbols / Candidates

| Symbol / Address | Current Interpretation |
|---|---|
| `DAT_0049` | Internal RAM/register flag byte seen in `BRCLR` context. |
| `MEAS_ENABLE_FLAGS_67` | Internal flag byte; bit `0x10` observed in measurement-enable logic. |
| `DAT_0192` | Internal RAM variable used near measurement/control logic. |
| `DAT_019B` | Internal RAM variable used near branch/condition logic. |
| `DAT_8249` | ROM constant / table byte observed as `0x1F`. |
| `0x2401 Bit7` | IC4 Port B Bit7 candidate, external A20 path. |

## Example Hardware Labels

```c
#define IC7_TIMER_BASE        0x2000
#define IC4_PIA_BASE          0x2400
#define IC3_INPUT_PORT        0x2800
#define RAM_EXT_BASE          0x2C00
#define ROM_EXT_BASE          0x8000
```

# Firmware Analysis Notes

## Current Target Model

The firmware is analyzed as an HC11-like ROM image, with external ROM mapped at `0x8000` and vectors at the top of the address space.

`IC1` is marked `SC402617FN`, so the exact CPU is not fully identified. The HC11 analysis model is useful but should not be treated as divine scripture carved into silicon by Motorola.

## Current Memory Map For Firmware Interpretation

| Range | Interpretation |
|---:|---|
| `0x0000-0x007F` | Internal MCU registers. |
| `0x0080-0x047F` | Internal RAM. |
| `0x0D80-0x0FFF` | Internal EEPROM. |
| `0x1000-0x10FF` | HC11 register model / analysis helper. |
| `0x2000-0x23FF` | `IC7` timer. |
| `0x2400-0x27FF` | `IC4` PIA. |
| `0x2800-0x2BFF` | `IC3` input port. |
| `0x2C00-0x2FFF` | External RAM window. |
| `0x3000-0x3FFF` | Unused / unassigned decoder range. |
| `0x8000-0xFFBF` | ROM program. |
| `0xFFC0-0xFFFF` | Vectors. |

## Known Firmware Symbols

| Symbol | Address / context | Notes |
|---|---|---|
| `MEAS_ENABLE_FLAGS_67` | `0x0067` | Flag byte. Used with `BRSET #0x10`. |
| `DAT_0049` | `0x0049` | Flag / state byte. Used with `BRCLR #0x01`. |
| `DAT_0192` | `0x0192` | RAM variable loaded in branch path. |
| `DAT_019B` | `0x019B` | RAM variable loaded before `BNE`. |
| `DAT_8249` | `0x8249` | ROM byte, observed value `0x1F`. |

## Example Code Fragment

```asm
BF6F: BRCLR  DAT_0049, #0x01, LAB_BF78
BF73: LDAA   DAT_019B
BF76: BNE    LAB_BF8A
BF78: LDAA   DAT_0192
BF7B: BRSET  MEAS_ENABLE_FLAGS_67, #0x10, LAB_BF82
BF7F: LDAA   DAT_8249
```

Interpretation pending. This fragment appears to select between RAM state variables and a ROM fallback / calibration value depending on flags.

## Hardware Register Interpretation Notes

### `0x2401 Bit 7`

Current conclusion:

- `0x2401` is likely `IC4` Port B / DDRB space due to mirrored RS wiring.
- Bit 7 likely corresponds to `IC4 pin17`.
- `IC4 pin17` has `R30` pull-up and external connector `A20`.
- It is not currently interpreted as `CRA bit 7 / CA1`.
- `IC4 pin40 CA1` is tied to GND.

### `IC7` Timer

The timer is selected at `0x2000-0x23FF`, with outputs:

| Timer output | Physical route |
|---|---|
| `O1` | `IC7:27 -> C14 -> IC701:4`. |
| `O2` | `IC7:3 -> C15 -> IC701:5`. |
| `O3` | `IC7:6 -> C16 -> IC701:6`. |

Because `/IRQ` is not connected, firmware likely configures and polls the timer, while outputs perform hardware timing.

## Analysis Workflow Notes

- Label known hardware addresses before naming high-level functions.
- Keep uncertain functions as `SUB_xxxx`.
- Do not assume the PIA register order from the datasheet without applying the actual RS wiring.
- Treat reads from `IC3_INPUT_PORT` as polarity-dependent because `IC6`/`IC500` channels differ in behavior.
- Watch for timer setup code writing to `0x2000-0x23FF`; it may reveal injection timing configuration.
- Watch for port writes to `0x2400-0x2403`; they may configure SE123 output directions / enables.

## Suggested Next Firmware Tasks

1. Locate reset vector and startup initialization.
2. Identify all writes to `IC4` PIA control registers.
3. Identify all writes to `IC7` timer control registers.
4. Cross-reference all reads from `IC3_INPUT_PORT`.
5. Label RAM variables that gate injector / measurement / enable behavior.
6. Build a table of memory-mapped I/O accesses by address and function.

# Ghidra HC11 Setup

## Goal

Use Ghidra to analyze the ECU ROM image as an HC11-like firmware target.

The exact MCU is currently treated as `SC402617FN`, likely Denso-custom or HC11-derived enough for HC11-style disassembly workflows.

## Processor / Language Module

A third-party Ghidra processor / language module for Motorola `MC68HC11` / `68HC11` was used.

Exact library repository / release name: **TODO: add exact source URL or package name**.

Do not replace this with a guessed repository name. Reverse engineering already has enough fiction.

## Import Settings

Recommended working import model:

| Setting | Value / note |
|---|---|
| Architecture | Motorola 68HC11 / HC11-compatible language module. |
| Endianness | Big-endian for CPU instruction / address interpretation, per HC11 family behavior. |
| ROM base | `0x8000` for the external `27C256` image. |
| Vector area | `0xFFC0-0xFFFF`. |
| Main program ROM | `0x8000-0xFFBF`. |
| Boot ROM reference | `0xBE40-0xBFFF` appears in MCU reference material, but needs care in this external ROM image. |

## Memory Blocks

Create or verify memory blocks matching the working map:

| Address range | Block name | Type |
|---:|---|---|
| `0x0000-0x007F` | `MCU_REGS` | Internal registers. |
| `0x0080-0x047F` | `INTERNAL_RAM` | Internal RAM. |
| `0x0D80-0x0FFF` | `INTERNAL_EEPROM` | Internal EEPROM. |
| `0x1000-0x10FF` | `HC11_REG_MODEL` | Register model / compatibility area used during analysis. |
| `0x2000-0x23FF` | `IC7_TIMER` | HD63B40P timer. |
| `0x2400-0x27FF` | `IC4_PIA` | HD63BP21P PIA. |
| `0x2800-0x2BFF` | `IC3_INPUT_PORT` | 74HC541 input port. |
| `0x2C00-0x2FFF` | `EXT_RAM_WINDOW` | External SRAM window. |
| `0x3000-0x3FFF` | `UNUSED_DECODER_RANGE` | Currently unassigned. |
| `0x8000-0xFFBF` | `ROM` | Program ROM. |
| `0xFFC0-0xFFFF` | `VECTORS` | Interrupt / reset vectors. |

## Labeling Strategy

Use labels for known hardware registers and variables, but avoid over-renaming functions too early.

Practical workflow:

1. Let Ghidra identify code from reset vector and interrupt vectors.
2. Label hardware address references first.
3. Label known variable addresses when behavior is clear.
4. Use function names only when the function's role is understood.
5. Keep `SUB_xxxx` names where meaning is unknown.
6. Pressing `F` to create functions is useful where control flow clearly enters code, but do not blindly force functions into data tables.

## Known Labels / Addresses From Current Firmware Work

| Symbol / address | Meaning / note |
|---|---|
| `MEAS_ENABLE_FLAGS_67` | Firmware flag byte at `0x0067`, seen in `BRSET` context. |
| `DAT_0049` | Variable / flag byte referenced near `0xBF6F`. |
| `DAT_0192` | Variable loaded near `0xBF78`. |
| `DAT_019B` | Variable loaded near `0xBF73`. |
| `DAT_8249` | ROM data byte observed as `0x1F`, loaded near `0xBF7F`. |
| `0x2401 bit 7` | Now interpreted as likely PIA Port B bit 7 / external `A20`, not CRA/CA1. |

Example observed disassembly context:

```asm
BF6F: BRCLR  DAT_0049, #0x01, LAB_BF78
BF73: LDAA   DAT_019B
BF76: BNE    LAB_BF8A
BF78: LDAA   DAT_0192
BF7B: BRSET  MEAS_ENABLE_FLAGS_67, #0x10, LAB_BF82
BF7F: LDAA   DAT_8249   ; observed ROM byte: 0x1F
```

## Hardware Address Labels

Suggested Ghidra labels:

| Address | Suggested label |
|---:|---|
| `0x2000` | `IC7_TIMER_BASE` |
| `0x2400` | `IC4_PIA_BASE` |
| `0x2400` | `IC4_PORTA_OR_DDRA` |
| `0x2401` | `IC4_PORTB_OR_DDRB` |
| `0x2402` | `IC4_CRA` |
| `0x2403` | `IC4_CRB` |
| `0x2800` | `IC3_INPUT_PORT` |
| `0x2C00` | `EXT_RAM_BASE` |

Because of the mirrored `IC4` register select wiring, use the labels above rather than generic PIA datasheet order.

## Decompiler Caveats

The decompiler may produce misleading output when:

- RAM/register overlays are not modeled correctly,
- hardware registers are treated as normal RAM,
- vector tables or jump tables are misidentified,
- memory-mapped I/O reads have side effects,
- port/DDRx switching in PIA control registers changes register meaning.

For I/O-heavy firmware, the disassembly is often more trustworthy than decompiled C.

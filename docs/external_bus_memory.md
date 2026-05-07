# External Bus and Memory Map

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Memory Map / Ghidra Block Recommendation
### Confirmed / Strongly Supported External Map

| Address Range | Name | Component | Select Source | Access | Execute | Volatile | Notes |
|---|---|---|---|---|---|---|---|
| `0x2000-0x23FF` | `IC7_TIMER_BASE` | IC7 `HD63B40P` | IC17 `/Y0` | R/W | No | Yes | Timer/peripheral. |
| `0x2400-0x27FF` | `IC4_PIA_BASE` | IC4 `HD63BP21P` | IC17 `/Y1` | R/W | No | Yes | PIA / parallel I/O. |
| `0x2800-0x2BFF` | `IC3_INPUT_PORT` | IC3 `74HC541` | IC17 `/Y2` + global `/OE` | Read only | No | Yes | Input port from IC500/IC6. |
| `0x2C00-0x2FFF` | `RAM_EXT` | IC10 `TC5564` RAM window | IC17 `/Y3` | R/W | No | No | External SRAM window. |
| `0x3000-0x3FFF` | `UNUSED_DECODER_SPACE` | Not wired | IC17 `/Y4-/Y7` not connected | None | No | N/A | Decoder outputs not wired. |
| `0x8000-0xFFFF` | `ROM_EXT` | IC11 `27C256` | ROM `/CE` via IC20 | Read | Yes | No | Probable 32 KiB EPROM mapping. Confirm with reset vector. |

### Internal MCU Areas from Related Datasheet Information

| Address Range | Name | Access | Execute | Volatile | Notes |
|---|---|---|---|---|---|
| `0x0000-0x007F` | `MCU_INTERNAL_REGS` | R/W | No | Yes | Internal MCU register range, based on related datasheet information. |
| `0x0080-0x047F` | `MCU_INTERNAL_RAM` | R/W | No | No | Internal MCU RAM, based on related datasheet information. |
| `0x0D80-0x0FFF` | `MCU_INTERNAL_EEPROM` | R/W | No | No | Internal EEPROM range, based on related datasheet information. |

### Useful Ghidra Labels

```c
#define IC7_TIMER_BASE        0x2000
#define IC4_PIA_BASE          0x2400
#define IC3_INPUT_PORT        0x2800
#define RAM_EXT_BASE          0x2C00
#define ROM_EXT_BASE          0x8000

#define IC3_BIT_IC500_PIN11   0x01
#define IC3_BIT_IC500_PIN10   0x02
#define IC3_BIT_IC500_PIN6    0x04
#define IC3_BIT_IC6_PIN8      0x08
#define IC3_BIT_IC500_PIN4    0x10
#define IC3_BIT_IC500_PIN8    0x20
#define IC3_BIT_IC500_PIN7    0x40
#define IC3_BIT_IC500_PIN3    0x80
```

---


## Address Bus Mapping
| Signal | Connections |
|---|---|
| A14 | IC11:27, IC17:5, IC1:15, Pull-up R57 |
| A13 | IC11:26, IC1:16, IC17:6, Pull-up R58 |
| A12 | IC11:2, IC10:2, IC1:17, IC17:3, Pull-up R59 |
| A11 | IC11:23, IC10:23, IC1:18, IC17:2, Pull-up R60 |
| A10 | IC11:21, IC10:21, IC1:19, IC17:1, Pull-up R61 |
| A9 | IC11:24, IC10:24, IC1:20, Pull-up R62 |
| A8 | IC11:25, IC10:25, IC1:21, Pull-up R63 |
| A7 | IC11:3, IC10 shared address bus, IC1:71 | Earlier mapping; verify exact RAM pin if needed. |
| A6 | IC11:4, IC10 shared address bus, IC1:72 | Earlier mapping; verify exact RAM pin if needed. |
| A5 | IC11:5, IC10 shared address bus, IC1:73 | Earlier mapping; verify exact RAM pin if needed. |
| A4 | IC11:6, IC10 shared address bus, IC1:74 | Earlier mapping; verify exact RAM pin if needed. |
| A3 | IC11:7, IC10 shared address bus, IC1:75 | Earlier mapping; verify exact RAM pin if needed. |
| A2 | IC11:8, IC10 shared address bus, IC1:76 | Earlier mapping; verify exact RAM pin if needed. |
| A1 | IC11:9, IC10 shared address bus, IC1:77 | Earlier mapping; verify exact RAM pin if needed. |
| A0 | IC11:10, IC10 shared address bus, IC1:78 | Earlier mapping; verify exact RAM pin if needed. |

---


## Data Bus Mapping
| Signal | Pull-up | IC1 Pin | IC10 SRAM | IC11 EPROM | IC7 | IC4 | IC3 |
|---|---|---:|---|---|---|---|---|
| D0 | R55 | 28 | Pin 11 | Pin 11 | Pin 25 | Pin 33 | Pin 11 / Y7 |
| D1 | R54 | 29 | Pin 12 | Pin 12 | Pin 24 | Pin 32 | Pin 12 / Y6 |
| D2 | R53 | 30 | Pin 13 | Pin 13 | Pin 23 | Pin 31 | Pin 13 / Y5 |
| D3 | R52 | 31 | Pin 15 | Pin 15 | Pin 22 | Pin 30 | Pin 14 / Y4 |
| D4 | R51 | 32 | Pin 16 | Pin 16 | Pin 21 | Pin 29 | Pin 15 / Y3 |
| D5 | R50 | 33 | Pin 17 | Pin 17 | Pin 20 | Pin 28 | Pin 16 / Y2 |
| D6 | R49 | 34 | Pin 18 | Pin 18 | Pin 19 | Pin 27 | Pin 17 / Y1 |
| D7 | R48 | 35 | Pin 19 | Pin 19 | Pin 18 | Pin 26 | Pin 18 / Y0 |

---


## Access Flags for Ghidra

| Region | Read | Write | Execute | Volatile | Comment |
|---|---:|---:|---:|---:|---|
| `MCU_INTERNAL_REGS` | Yes | Yes | No | Yes | Internal CPU registers. |
| `MCU_INTERNAL_RAM` | Yes | Yes | No | No | Internal RAM. |
| `MCU_INTERNAL_EEPROM` | Yes | Yes | No | No | Internal EEPROM. |
| `IC7_TIMER_BASE` | Yes | Yes | No | Yes | Hardware timer registers. |
| `IC4_PIA_BASE` | Yes | Yes | No | Yes | Hardware PIA registers. |
| `IC3_INPUT_PORT` | Yes | No | No | Yes | Read-only live input buffer. |
| `RAM_EXT` | Yes | Yes | No | No | External SRAM window. |
| `ROM_EXT` | Yes | No | Yes | No | EPROM code block. |

## Important Caveat

The related datasheet internal map and the board-level external decode can both be true. The external decode only appears when the MCU asserts the external bus/select conditions. Internal MCU regions remain internal even if the numeric address range looks close enough to invite confusion, because apparently address maps enjoy psychological warfare.

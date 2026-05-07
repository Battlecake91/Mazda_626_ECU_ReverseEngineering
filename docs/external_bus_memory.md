# External Bus and Memory

## External data bus

The external data bus is 8-bit wide and shared by ROM, RAM, buffer, and memory-mapped peripherals.

| Signal | IC1 pin | Notes |
|---|---:|---|
| `D0` / `IO0` | 49 | ROM + RAM data bus |
| `D1` / `IO1` | 50 | ROM + RAM data bus |
| `D2` / `IO2` | 51 | ROM + RAM data bus |
| `D3` / `IO3` | 52 | ROM + RAM data bus |
| `D4` / `IO4` | 53 | ROM + RAM data bus |
| `D5` / `IO5` | 54 | ROM + RAM data bus |
| `D6` / `IO6` | 55 | ROM + RAM data bus |
| `D7` / `IO7` | 56 | ROM + RAM data bus |

## External address bus

| Signal | IC1 pin | Used by |
|---|---:|---|
| `A0` | 78 | ROM/RAM/peripherals |
| `A1` | 77 | ROM/RAM/peripherals |
| `A2` | 76 | ROM/RAM/peripherals |
| `A3` | 75 | ROM/RAM |
| `A4` | 74 | ROM/RAM |
| `A5` | 73 | ROM/RAM |
| `A6` | 72 | ROM/RAM |
| `A7` | 71 | ROM/RAM |
| `A8` | 21 | ROM/RAM |
| `A9` | 20 | ROM/RAM |
| `A10` | 19 | ROM/RAM/decoder |
| `A11` | 18 | ROM/RAM/decoder |
| `A12` | 17 | ROM/RAM/decoder |
| `A13` | 16 | ROM and decoder |
| `A14` | 15 | ROM and decoder |

## ROM: IC11 27C256

Known pins:

| IC11 pin | Signal |
|---:|---|
| 3..10 | `A7..A0` |
| 11..13 | `O0..O2` |
| 15..19 | `O3..O7` |
| 20 | `/CE` or `/PGM` context; connected to `IC20:11` |
| 21 | `A10` |
| 22 | `/OE`, shared global read `/OE` |
| 23 | `A11` |
| 24 | `A9` |
| 25 | `A8` |
| 26 | `A13` |
| 27 | `A14` |

## RAM: IC10 TC5564APL

Known pins:

| IC10 pin | Signal / connection |
|---:|---|
| 2 | `A12`, also `IC17:3` and `IC1:17` |
| 20 | `IC17:10`, likely RAM chip-select path |
| 22 | shared global `/OE`, also `IC20:3` and `IC3:19` |
| 26 | `CE2`, connected to `IC20:2`, `IC7:17`, `IC4:25`, via `F4` to `IC1:48` |
| 27 | `R/W`, connected to `IC20:1`, `IC4:21`, `IC7:13`, via `F3` to `IC1:47` |

## Memory-map caution

A previous assumption that ROM starts at `0x4000` was challenged. Do **not** treat `0x4000` as proven.

Current safer statement:

> ROM placement must be derived from reset-vector location, 27C256 wiring, `IC17` decode, and valid code flow in Ghidra.

## Provisional Ghidra memory types

| Region type | Read | Write | Volatile | Notes |
|---|---:|---:|---:|---|
| ROM / `IC11` | yes | no | no | base address still TODO |
| RAM / `IC10` | yes | yes | yes-ish | variables, stack, buffers |
| PIA/PTM/peripheral registers | yes | yes | yes | memory-mapped I/O |
| `IC3` input buffer | yes | no | yes | address-selected external input read |

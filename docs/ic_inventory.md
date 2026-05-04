# IC Inventory

## Main IC list

| Reference | Device / marking | Package / notes | Current role |
|---|---|---|---|
| IC1 | SC402617FN | Large MCU/custom IC | Main CPU / bus master |
| IC3 | 74HC541 | Octal buffer | Data bus input buffer |
| IC4 | HD63BP21P | PIA-type peripheral | Parallel I/O / interrupt control |
| IC5 | SE123 | DIP-24 | Custom driver device, chip 1 |
| IC6 | 151821-0020 | Custom Denso IC | Unknown peripheral / interface |
| IC7 | HD63B40P | Hitachi peripheral | Bus-connected peripheral/timer candidate |
| IC8 | 74HC595 | Shift register | Serial-to-parallel output expansion |
| IC9 | 74HC74AP | Dual D flip-flop | State/control logic |
| IC10 | TC5564APL | SRAM | 8 KiB external RAM |
| IC11 | 27C256 | EPROM | 32 KiB external program/data ROM |
| IC13 | TC4051BP | Analog mux | Analog signal multiplexing |
| IC17 | 74HC138AP | 3-to-8 decoder | Chip select decoding |
| IC20 | 74HC00AP | Quad NAND | Bus/control glue logic |
| IC21 | TLC272 | Dual op amp | Analog signal conditioning |
| IC800 | SE123 | DIP-24 | Custom driver device, chip 2 |
| X1 | 8 MHz crystal | Clock component | MCU clock source |

## Memory and bus ICs

### IC10: TC5564APL SRAM

- 8 KiB static RAM.
- Shares address lines with ROM up to at least `A12`.
- Uses `CE2`, `/OE` and `R/W` style control.
- Known relevant pins:
  - `IC10 pin 19` connected to `IC11 pin 19` and `IC4 pin 26` context.
  - `IC10 pin 22` is `/OE`, connected to global `/OE` line through `F2` / `IC20 pin 3`.
  - `IC10 pin 26` is `CE2`, connected to the `E`/bus-enable net.
  - `IC10 pin 27` is `R/W`, connected to the `R/W` net.

### IC11: 27C256 EPROM

- 32 KiB EPROM.
- Known datasheet pin mapping:
  - `pin 3..10` = `A7..A0`
  - `pin 11..13` = `O0..O2`
  - `pin 15..19` = `O3..O7`
  - `pin 19` = `O7`
  - `pin 20` = `/CE` or `/PGM`
  - `pin 21` = `A10`
  - `pin 22` = `/OE`
  - `pin 23` = `A11`
  - `pin 24` = `A9`
  - `pin 25` = `A8`
  - `pin 26` = `A13`
  - `pin 27` = `A14`

### IC3: 74HC541

- Output pins `11..18` are connected to data bus `D0..D7` / `IO0..IO7`.
- Inputs come from local logic and from a flat-flex connection to the second board.
- The device is likely memory-mapped through `IC17` and enabled only during read cycles.

## Custom / unknown ICs

### SE123: IC5 and IC800

Current hypothesis: custom Denso multi-channel output driver, likely low-side or open-collector/open-drain style with logic-side pins and 12 V/output-side pins.

See [SE123 driver devices](se123_driver_devices.md).

### IC6 / IC500: 151821-0020

- `IC6` is on the main board.
- `IC500` appears on the second board and is described as functionally or physically similar to `IC6`.
- Several `IC3` input lines route through the connector to `IC500`, suggesting secondary-board signals are buffered onto the main data bus.

# IC Inventory

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Integrated Circuits
| RefDes | Marking / Part | Package / Type | Known / Inferred Function | Notes |
|---|---|---|---|---|
| IC1 | `SC402617FN` | MCU / custom controller | Main MCU / custom CPU | External address/data bus, 8 MHz clock. |
| IC3 | `74HC541` | Octal buffer | Memory-mapped input port | Outputs connected to D0-D7. Selected by IC17 `/Y2`. Reads status from IC500/IC6. |
| IC4 | `HD63BP21P` | PIA | Parallel I/O Adapter | Mapped at `0x2400-0x27FF` via IC17 `/Y1`. Register select is **non-standard/spiegeled**: IC4 Pin35 RS1 = A0, Pin36 RS0 = A1. |
| IC5 | `SE123` | DIP-24 custom Denso IC | Probable multi-channel output driver / low-side or open-collector style driver | 12 V side likely outputs. See SE123 notes. |
| IC6 | `151821-0020` / `D151821-0020` | DIP-24 custom Denso IC | Comparator / 12 V to 5 V level shifter | Mixed comparator, ground-detect and 12 V-detect behavior. |
| IC7 | `HD63B40P` | Timer / counter peripheral | Timer / peripheral IC | Mapped at `0x2000-0x23FF` via IC17 `/Y0`. RS0/RS1/RS2 = A0/A1/A2. Timer outputs O1/O2/O3 routed externally. |
| IC8 | `74HC595` | Shift register | Serial-to-parallel output register | Exact downstream function not yet fully mapped. |
| IC9 | `74HC74AP` | Dual D flip-flop | Two-stage divider for IC7 timer clock | Divides E / bus-enable clock down; output on IC9 Pin5 feeds IC7 clock inputs via F1. |
| IC10 | `TC5564APL` | SRAM, 8K x 8 | External RAM | Visible window selected at `0x2C00-0x2FFF` via IC17 `/Y3`. |
| IC11 | `27C256` | EPROM, 32 KiB | Program ROM | Likely mapped at `0x8000-0xFFFF`; confirm with reset vector at dump offset `0x7FFE/0x7FFF`. |
| IC13 | `TC4051BP` | 8-channel analog mux/demux | Analog multiplexing | Exact channels not fully mapped. |
| IC17 | `74HC138AP` | 3-to-8 decoder | External peripheral decoder | Decodes A10-A12 with A13/A14 and IC1:14 select. |
| IC20 | `74HC00AP` | Quad NAND | Bus read/OE and select logic | Generates global `/OE` from R/W and E; ROM `/CE` inverter; other signal inverters. |
| IC21 | `TLC272` | Dual op-amp | Analog conditioning | Package noted as FK in project notes. |
| IC800 | `SE123` | DIP-24 custom Denso IC | Probable multi-channel output driver / low-side or open-collector style driver | Second SE123 device. |
| IC001 | `SE134` | 12-pin single-row Denso IC, secondary board | Unknown Denso custom IC | Supply pins known; connected with reset / external board logic. |
| IC250 | `MP611` | Denso custom IC, secondary board | Unknown interface / logic IC | Supply pins known; connected to A20 / IC001 and SE074 common lines. |
| IC500 | `151821-0020` / `D151821-0020` | DIP-24 custom IC, secondary board | Same family / behavior as IC6 | Connected to IC3 input buffer via ribbon cable. |
| IC700 | `SE074` | Denso DIP-16, secondary board | Likely 3-channel injector pre-driver / driver-logic IC | Drives T711/T731/T751 external injector outputs. |
| IC701 | `SE074` | Denso DIP-16, secondary board | Likely 3-channel injector pre-driver / driver-logic IC | Receives IC7 timer outputs O1/O2/O3 and drives T721/T741/T761 outputs. |

---


## Notes

- Denso custom devices (`SE123`, `SE074`, `SE134`, `MP611`, `D151821-0020`) are documented by measured wiring and behavior, not by official datasheets.
- IC4 and IC7 are standard Hitachi/Motorola-style peripherals, but wiring matters more than textbook assumptions. Naturally, the board chose violence and swapped IC4 register select expectations.

# KLDE ECU Reverse Engineering BOM / Working Hardware Map

This document collects the currently known component population, measured values, connector pins, bus wiring and memory-map findings for the Mazda 626 KLDE / KL05 ECU reverse-engineering project.

> Working document, not an official Mazda/Denso BOM. Confidence varies per row. Measurements and corrections from the project take priority over internet pinouts.

---

## Identification

| Item | Marking / Part Number | Notes |
|---|---|---|
| ECU / Control Unit | `U2103136866B` | Control unit identification found on the ECU. |
| ECU family / readable marking | `KL05` | Only `KL05` is still readable on the ECU label. Remaining serial / part number is scratched and unreadable. |
| PCB / sub-board / interface board | `079721-3521` | Likely Denso / PCB / sub-board number for switching stages, voltage dividers, interface or secondary board circuitry. |
| Vehicle / engine context | Mazda 626 KLDE | KL-series V6 ECU, likely European KL05 variant. |

---

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

## Clock / Oscillator

| RefDes | Value / Marking | Type | Notes |
|---|---|---|---|
| X1 | 8 MHz | Crystal | Main clock crystal. |
| C1 | 40 pF | 0805 C0G | Likely crystal load capacitor. |
| C2 | 40 pF | 0805 C0G | Likely crystal load capacitor. |

---

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

## IC3 74HC541 Input Buffer Mapping

IC3 appears to be a memory-mapped 8-bit input buffer selected at `0x2800-0x2BFF`. It has no latch; it transparently buffers external status signals onto the data bus during selected read cycles.

### Data Bus Side

| Signal | IC3 Pin | Notes |
|---|---:|---|
| D0 | 11 | Data bus bit 0 |
| D1 | 12 | Data bus bit 1 |
| D2 | 13 | Data bus bit 2 |
| D3 | 14 | Data bus bit 3 |
| D4 | 15 | Data bus bit 4 |
| D5 | 16 | Data bus bit 5 |
| D6 | 17 | Data bus bit 6 |
| D7 | 18 | Data bus bit 7 |

### Input Side / Source Mapping

| CPU Bit | Mask | IC3 Output Pin | 74HC541 Input | IC3 Input Pin | Source | Connection |
|---:|---:|---:|---|---:|---|---|
| D0 | `0x01` | 11 | A7 | 9 | IC500 Pin 11 | Ribbon connector B9 |
| D1 | `0x02` | 12 | A6 | 8 | IC500 Pin 10 | Ribbon connector A6 |
| D2 | `0x04` | 13 | A5 | 7 | IC500 Pin 6 | Ribbon connector B7 |
| D3 | `0x08` | 14 | A4 | 6 | IC6 Pin 8 | Direct |
| D4 | `0x10` | 15 | A3 | 5 | IC500 Pin 4 | Ribbon connector B6 |
| D5 | `0x20` | 16 | A2 | 4 | IC500 Pin 8 | Ribbon connector B5 |
| D6 | `0x40` | 17 | A1 | 3 | IC500 Pin 7 | Ribbon connector A4 |
| D7 | `0x80` | 18 | A0 | 2 | IC500 Pin 3 | Ribbon connector A5 |

### Raw IC3 Input Connections

| IC3 Input | Path | Destination |
|---|---|---|
| A0 | Ribbon A5 | IC500:3 |
| A1 | Ribbon A4 | IC500:7 |
| A2 | Ribbon B5 | IC500:8 |
| A3 | Ribbon B6 | IC500:4 |
| A4 | Direct | IC6:8 |
| A5 | Ribbon B7 | IC500:6 |
| A6 | Ribbon A6 | IC500:10 |
| A7 | Ribbon B9 | IC500:11 |

### Enables

| IC3 Pin | Signal | Connection | Notes |
|---:|---|---|---|
| 1 | `/OE1` | IC17 Pin 13 `/Y2` | Address select, active low. |
| 19 | `/OE2` | IC20 Pin 3 global `/OE` | Active during read cycles. |

---

## IC17 74HC138 Decoder

| IC17 Pin | 74HC138 Signal | Connected To | Notes |
|---:|---|---|---|
| 1 | A0 | A10 | Decoder input bit 0. |
| 2 | A1 | A11 | Decoder input bit 1. |
| 3 | A2 | A12 | Decoder input bit 2. |
| 4 | `/E1` | IC1:14, IC20:12/13, Pull-up R56 to VCC/IC20:14 | Active-low enable. Also drives ROM /CE inverter through IC20 gate 4. |
| 5 | `/E2` | A14, Pull-up R57 | Active-low enable. |
| 6 | E3 | A13, Pull-up R58 | Active-high enable. |
| 7 | `/Y7` | n.c. | Not wired. |
| 9 | `/Y6` | n.c. | Not wired. |
| 10 | `/Y5` | n.c. | Not wired. |
| 11 | `/Y4` | n.c. | Not wired. |
| 12 | `/Y3` | IC10 Pin 20 `/CE1` | External RAM window `0x2C00-0x2FFF`. |
| 13 | `/Y2` | IC3 Pin 1 `/OE1` | Input port window `0x2800-0x2BFF`. |
| 14 | `/Y1` | IC4 Pin 23 `/CS2` | PIA window `0x2400-0x27FF`. |
| 15 | `/Y0` | IC7 Pin 15 `/CS0` | Timer/peripheral window `0x2000-0x23FF`. |

IC17 is active when: `IC1:14 = 0`, `A14 = 0`, `A13 = 1`.

---

## IC20 74HC00 Bus / Select Logic

| Gate | IC20 Pins | Logic | Connections | Inferred Function |
|---|---|---|---|---|
| Gate 1 | 1,2 -> 3 | `NAND(R/W, E)` | Pin 1: R/W net; Pin 2: E net; Pin 3: IC10 `/OE`, IC11 `/OE`, IC3 `/OE2` | Generates global active-low read `/OE`. |
| Gate 2 | 4,5 -> 6 | Inverter | Pins 4+5 connected to IC6-related Schaltung 1; Pin 6 to IC1:9 | Inverts IC6-related status / comparator signal. |
| Gate 3 | 9,10 -> 8 | Inverter | Pins 9+10 to external C10 node; Pin 8 via R606 to IC1:11 | Timing / reset / status signal, unresolved. |
| Gate 4 | 12,13 -> 11 | Inverter | Pins 12+13 to IC1:14 / IC17 `/E1`; Pin 11 to IC11 `/CE` | ROM `/CE`; ROM active when IC1:14 is high. |

### IC20 Pin Connections

| IC20 Pin | Connection |
|---:|---|
| 1 | IC10:27 R/W, IC4:21 `/R/W`, IC7:13 `/R/W`, via F3 to IC1:47 |
| 2 | IC10:26 CE2, IC4:25 E, IC7:17 E, via F4 to IC1:48 |
| 3 | Via F2 to IC10:22 `/OE`, IC11:22 `/OE`, IC3:19 `/OE2` |
| 4+5 | Schaltung 1 / IC6-related node |
| 6 | IC1:9 |
| 8 | R606 -> IC1:11 |
| 9+10 | C10 external node |
| 11 | IC11:20 `/CE` |
| 12+13 | IC1:14, IC17:4 `/E1`, pull-up R56 |
| 14 | VCC |

### Schaltung 1 / IC20 Gate 2

| Node | Connection / Function |
|---|---|
| IC20:4+5 | NAND inputs tied together, acting as inverter input |
| IC6:2 | Probably Out1 |
| IC6:22 | Probably Input1 / comparator input |
| R99 | Pull-up at IC6:22 |
| R100 | Pull-down at IC6:22 |
| R101 / R43 | Part of divider / coupling network to IC6:22 / IC20 input |
| IC20:6 | Inverted output to IC1:9 |

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

## IC4 HD63BP21P PIA Notes

### Bus / Select

| IC4 Pin | Signal | Connection / Notes |
|---:|---|---|
| 21 | `/R/W` | R/W net, IC20:1, IC1:47 via F3 |
| 23 | `/CS2` | IC17 `/Y1`, maps IC4 to `0x2400-0x27FF` |
| 25 | E | E / bus-enable net, IC20:2, IC1:48 via F4 |
| 26-33 | D7-D0 | Connected to data bus |
| 35 | RS1 | **A0** |
| 36 | RS0 | **A1** |
| 34 | `/RESET` | Shared reset with IC7:8 and external D7 |
| 40 | CA1 | Hard GND |
| 37, 38 | n.c. | Not connected |

### Register Addressing

Because IC4 Pin35 RS1 = A0 and Pin36 RS0 = A1, the register order is mirrored compared with the default assumption.

| Address | A1 / RS0 | A0 / RS1 | Effective RS1:RS0 | Interpreted Register |
|---|---:|---:|---:|---|
| `0x2400` | 0 | 0 | 00 | Port A / DDRA |
| `0x2401` | 0 | 1 | 10 | Port B / DDRB |
| `0x2402` | 1 | 0 | 01 | CRA |
| `0x2403` | 1 | 1 | 11 | CRB |

**Important correction:** A firmware access to `0x2401` Bit7 is therefore not CRA Bit7 / CA1. It is most likely Port B Bit7 = IC4 Pin17, which has R30 pull-up and goes to external A20. IC4 CA1 is Pin40 and is tied hard to GND.

### IC4 Observed Pin List

| IC4 Pin | Connection / Notes |
|---:|---|
| 7 | R39 pull-up + R42 pull-down |
| 8 | R38 pull-up + R41 pull-down |
| 9 | R37 pull-up + R40 pull-down |
| 10 | R36 pull-up + IC5:6 |
| 11 | R35 pull-up + IC5:10 |
| 12 | R34 pull-up + IC5:8 |
| 13 | R33 pull-up + IC5:9 |
| 14 | R32 pull-up + IC5:7 |
| 15 | R31 pull-up + IC800:7 |
| 16 | External B8 |
| 17 | R30 pull-up + external A20 |
| 18 | GND |
| 19 | R28 pull-down |
| 21 | R/W net |
| 22 | R26 pull-down |
| 23 | IC17:14 `/Y1` |
| 24 | R27 pull-down |
| 29 | R25 pull-down |
| 34 | `/RESET`, shared with IC7:8 and external D7 |
| 37, 38 | n.c. |
| 40 | CA1 hard GND |

---

## IC7 HD63B40P Timer Notes

### Bus / Select / Clock

| IC7 Pin | Signal | Connection / Notes |
|---:|---|---|
| 10 | RS0 | A0 |
| 11 | RS1 | A1 |
| 12 | RS2 | A2 |
| 13 | `/R/W` | R/W net, IC20:1, IC1:47 via F3 |
| 15 | `/CS0` | IC17 `/Y0`, maps IC7 to `0x2000-0x23FF` |
| 16 | CS1 | VCC, permanently enabled |
| 17 | E | E / bus-enable net, IC20:2, IC1:48 via F4 |
| 18 | GND | Ground |
| 8 | `/RESET` | IC4:34 and external D7 |
| 9 | `/IRQ` | Not connected |
| 14 | VCC | Supply |

### Gate / Clock / Output Pins

| IC7 Pin | Signal / Function | Connection / Notes |
|---:|---|---|
| 2 | `/G2` | GND |
| 5 | `/G3` | GND |
| 26 | `/G1` | GND |
| 4 | `/C2` | Connected with IC7:7 and IC7:28 via F1 to IC9:5 |
| 7 | `/C3` | Connected with IC7:4 and IC7:28 via F1 to IC9:5 |
| 28 | `/C1` | Connected with IC7:4 and IC7:7 via F1 to IC9:5 |
| 27 | O1 | External C14 with pull-up R85/R86 context; routed to IC701:4 |
| 3 | O2 | External C15 with pull-up R86/R85 context; routed to IC701:5 |
| 6 | O3 | External C16 with pull-up R87; routed to IC701:6 |

Timer outputs O1/O2/O3 are likely direct timer/pulse outputs used by the SE074 injector pre-driver logic on the secondary board.

---

## IC9 74HC74AP Clock Divider

IC9 is wired as a two-stage frequency divider for the IC7 timer clock.

| IC9 Pin | Function | Connection / Notes |
|---:|---|---|
| 1 | `/1CLR` | VCC |
| 13 | `/2CLR` | VCC |
| 11 | CLK2 | E / bus-enable net: IC4:25, IC10:26, IC20:2, IC7:17, F4 -> IC1:48 |
| 12 | D2 | Connected to Pin8 `/Q2`, so FF2 toggles |
| 8 | `/Q2` | Connected to Pin12 D2 |
| 9 | Q2 | Feeds IC9 Pin3 CLK1 |
| 3 | CLK1 | Fed from Pin9 Q2 |
| 2 | D1 | Connected to Pin6 `/Q1`, so FF1 toggles |
| 6 | `/Q1` | Connected to Pin2 D1 |
| 5 | Q1 | Via F1 to IC7 Pins4/7/28 clock inputs |
| 4 | `/1PRE` | VCC |
| 10 | `/2PRE` | VCC |

Result: E / bus-enable clocks FF2; FF2 produces E/2; FF1 produces E/4 on IC9:5/F1 for IC7 clocks.

---

## ECU Connector / Pin Function Working List

This table is a working reverse-engineered mapping and should be treated as project evidence, not an official Mazda pinout.

| ECU Pin | Function | Confidence | Notes |
|---|---|---|---|
| A | 12 V BAT | High | Fuse -> diode -> overvoltage protection. |
| B | 12 V from Main Relay | High | Fuse -> capacitor. Main relay is switched by ignition switch, not by ECU. |
| F | Small low-side output | Medium | 330 ohm low-side driver. Candidate: MIL / diagnostic / small output. |
| G | Distributor / ignition-related | Medium | 2.2 ohm low-side driver. Verify exact distributor function. |
| L | A/C relay | High | 2.2 ohm low-side relay output. Pulls condenser fan low via diode logic. |
| 2G | Fused/protected input | Low-Medium | Fuse -> unknown path; exact function unresolved. |
| 2O | Purge valve | High | 3.9 ohm 2 W low-side driver. |
| 2P | Engine fan high | High | No series resistor / 0 ohm low-side driver. |
| 3A | Low-side emitter / power-GND reference | High | Not an output. No direct connection to 3C/3D according to measurement. |
| 3B | Low-side emitter / power-GND reference | High | Treat as load driver ground reference. |
| 3C | GND | High | ECU / signal ground. |
| 3D | Air-flow sensor related | Medium | Verify ADC / input filtering path. |
| 3G | Distributor / ignition-related | Medium | 3.9 ohm low-side; verify against harness / distributor. |
| 3I | VRIS 2 provision | Medium | Unpopulated on this ECU. |
| 3J | VRIS 1 | High | 3.9 ohm low-side magnetic valve class. |
| 3L | Engine fan low / condenser fan low via diode logic | Medium-High | 0 ohm low-side special relay output. |
| 3M | Fuel pressure solenoid valve | High | 3.9 ohm low-side magnetic valve class. |
| 3N | Condenser fan high | High | 0 ohm low-side special relay output. |
| 3O | EGR open solenoid | High | 3.9 ohm low-side magnetic valve class. |
| 3P | EGR close solenoid | High | 3.9 ohm low-side magnetic valve class. |
| 3Q | Idle Air Control Valve | Very High | Strong low-side / PWM-capable output. Earlier mistaken as 3A. |
| 3T | Fuel pump relay | Very High | 2.2 ohm low-side relay output. |
| 3U | Injector 1 | Very High | 82 ohm injector low-side driver group. |
| 3V | Injector 2 | Very High | 82 ohm injector low-side driver group. |
| 3W | Injector 3 | Very High | 82 ohm injector low-side driver group. |
| 3X | Injector 4 | Very High | 82 ohm injector low-side driver group. |
| 3Y | Injector 5 | Very High | 82 ohm injector low-side driver group. |
| 3Z | Injector 6 | Very High | 82 ohm injector low-side driver group. |

---

## Output Driver Classes

| Class | Pins / Signals | Notes |
|---|---|---|
| Injector low-side drivers | 3U, 3V, 3W, 3X, 3Y, 3Z | 82 ohm driver path. |
| 3.9 ohm magnetic valve outputs | 2O, 3J, 3M, 3O, 3P, possibly 3G | VRIS, purge, FPRC, EGR solenoids. |
| 2.2 ohm relay / stronger outputs | G, L, 3T | Distributor/ignition or relay outputs. |
| 0 ohm / no series resistor special outputs | 2P, 3L, 3N | Fan relay / special outputs. |
| Strong PWM output | 3Q | Idle-air control valve. |
| Small low-side output | F | 330 ohm low-side driver. Candidate MIL/diagnostic. |
| Unpopulated provision | 3I | VRIS 2 provision, unpopulated on this ECU. |
| Low-side emitter / driver ground reference | 3A, 3B | Not outputs; driver-emitter / load-ground reference area. |

Total counted low-side/output driver population from current project state: about 19 active low-side drivers, including 6 injectors and 13 non-injector outputs. 3I is a provision but unpopulated on this ECU.

---

## SE123 Notes

The SE123 devices are DIP-24 custom Denso-style parts. Current hypothesis: multi-channel output driver / low-side or open-collector style driver.

### Common Pin Context

| Pin | Observed Function |
|---:|---|
| 1 | VCC |
| 11 / 12 | GND |
| 13 | VCC |
| 24 | 12 V |

### Hypothesized Channel Mirroring

| Logic Side | 12 V / Output Side |
|---:|---:|
| 10 | 14 |
| 9 | 15 |
| 8 | 16 |
| 7 | 17 |
| 6 | 18 |
| 5 | 19 |
| 4 | 20 |
| 3 | 21 |
| 2 | 22 |

### IC5 SE123 Output Side Mapping

| IC5 Pin | ECU / Net Context |
|---:|---|
| 14 | C4 |
| 15 | C11 |
| 16 | C12 |
| 17 | D16 |
| 18 | D14 |
| 19 | A10 |
| 20 | B12 |
| 21 | A1 |
| 22 | n.c. |
| 23 | n.c. |

### IC800 SE123 Output Side Mapping

| IC800 Pin | ECU / Net Context |
|---:|---|
| 14 | C4 |
| 15 | D12 |
| 16 | D19 |
| 17 | D18 |
| 18 | A9 |
| 19 | C20 |
| 20 | D13 |
| 21 | D15 |
| 22 | C19 |
| 23 | n.c. |

---

## D151821-0020 / IC6 / IC500 Notes

Likely Denso comparator / 12 V to 5 V level-shifter device, DIP-24.

| Pin | Function / Behavior |
|---:|---|
| 1 | Vin +12 V |
| 12 | GND |
| 24 | Vin +5 V |
| 2-11 | Outputs 1-10, 5 V logic side |
| 23 | Input0 / comparator reference / input |
| 22 | Input1 / comparator input |
| 21 | Input2 |
| 20 | Input3 |
| 19 | Input4 |
| 18 | Input5 |
| 17 | Input6 |
| 16 | Input7 |
| 15 | Input8 |
| 14 | Input9 |
| 13 | Input10 |

Behavior noted from external source / project validation:

- Output1 Pin 2 is comparator output for Pin22 against Pin23.
- Pin2 pulls low when Pin22 voltage is lower than Pin23.
- Pin2 is open / high impedance when Pin22 is higher than Pin23.
- Outputs2-8 are +5 V with floating input and 0 V when corresponding input is grounded.
- Outputs9-10 are 0 V with floating input and +5 V when +12 V is applied to corresponding input.

---

## Secondary Board Denso IC Supply Pins

| IC | +5 V | GND | +12 V | Notes |
|---|---|---|---|---|
| IC001 SE134 | Pin 7 | Pin 6 | Pins 10, 11 | 12-pin single-row Denso IC. |
| IC250 MP611 | Pin 12 | Pins 3, 15 | - | Denso custom IC. |
| IC700 SE074 | Pin 16 | Pins 3, 8, 14 | Pin 9 | Likely 3-channel injector pre-driver/driver-logic IC. |
| IC701 SE074 | Pins 3, 14, 16 | Pin 8 | Pin 9 | Likely 3-channel injector pre-driver/driver-logic IC. |

---

## SE074 / Injector Secondary Board Working Hypothesis

Current hypothesis: IC700 and IC701 SE074 are very likely 3-channel injector pre-driver / driver-logic ICs. The internal Denso transistor numbering is treated as more reliable than uncertain US wiring-plan injector numbering.

### IC701 SE074

| IC701 Pin | Connection / Function |
|---:|---|
| 1 + 2 | Connected with IC700:1+2, external C3, and IC250:19; likely common enable/diagnose/sense/mode |
| 4 | From external C14 <- IC7:27/O1 with pull-up |
| 5 | From external C15 <- IC7:3/O2 with pull-up |
| 6 | From external C16 <- IC7:6/O3 with pull-up |
| 7 | External C17 -> IC1:6 with R7 pull-up; possible enable/bank/mode |
| 10 | R825 -> T801:1; T801:2 to external GND 3C/3D; T801:3 has pull-up / divider / tantalum network |
| 11 | R763 -> T761 -> external 3Z; likely injector output group |
| 12 | R743 -> T741 -> external 3X; likely injector output group |
| 13 | R723 -> T721 -> external 3V; likely injector output group |

### IC700 SE074

| IC700 Pin | Connection / Function |
|---:|---|
| 1 + 2 | Connected with IC701:1+2, external C3, and IC250:19 |
| 4 | External C5 -> IC1:5 with R8 pull-up |
| 5 | External C6 -> IC1:4 with R9 pull-up |
| 6 | External D6 -> IC1:3 with R10 pull-up |
| 7 | External C8 -> IC1:26 with R18 pull-up |
| 11 | R753 -> T751 -> external 3Y; likely injector output group |
| 12 | R733 -> T731 -> external 3W; likely injector output group |
| 13 | R713 -> T711 -> external 3U; likely injector output group |

### Injector Group Hypothesis

| Internal Driver | External Pin | Working Function |
|---|---|---|
| T711 | 3U | Injector 1 candidate |
| T721 | 3V | Injector 2 candidate |
| T731 | 3W | Injector 3 candidate |
| T741 | 3X | Injector 4 candidate |
| T751 | 3Y | Injector 5 candidate |
| T761 | 3Z | Injector 6 candidate |

Open point: IC700 receives comparable control lines directly from IC1 rather than IC7 O1/O2/O3, so the exact bank/channel/phase logic remains to be determined.

---

## Resistors

### 10 kOhm, 0805

| RefDes |
|---|
| R29 |
| R15 |
| R87 |
| R85 |
| R75 |
| R20 |
| R5 |
| R82 |
| R83 |
| R84 |
| R601 |
| R602 |
| R56 |
| R613 |
| R611 |
| R68 |
| R59 |
| R64 |
| R65 |
| R66 |
| R67 |
| R70 |
| R71 |
| R69 |
| R60 |
| R61 |
| R62 |
| R63 |
| R4 |
| R58 |
| R3 |
| R57 |
| R6 |
| R10 |
| R11 |
| R9 |
| R8 |
| R7 |
| R2 |
| R13 |
| R18 |
| R14 |
| R48 |
| R49 |
| R50 |
| R52 |
| R55 |
| R51 |
| R53 |
| R54 |
| R73 |
| R97 |
| R98 |
| R22 |
| R23 |
| R30 |
| R19 |
| R17 |
| R16 |
| R21 |
| R155 |
| R38 |
| R39 |
| R40 |
| R36 |
| R35 |
| R34 |
| R33 |
| R605 |
| R25 |
| R31 |
| R32 |
| R28 |

### Other Known Resistors

| RefDes | Value | Package | Notes |
|---|---:|---|---|
| R606 | 1 kOhm | 0805 | IC20:8 to IC1:11 path. |
| R44 | 1 kOhm | 0805 |  |
| R45 | 1 kOhm | 0805 |  |
| R46 | 1 kOhm | 0805 |  |
| R47 | 1 kOhm | 0805 |  |
| R96 | 1 kOhm | 0805 |  |
| R95 | 75 kOhm | 0805 |  |
| R612 | 75 kOhm | 0805 |  |
| R43 | 5.1 kOhm | 0805 | Part of IC6/IC20 Schaltung 1 area. |
| R99 | 5.1 kOhm | 0805 | Pull-up at IC6:22 in Schaltung 1. |
| R100 | 5.1 kOhm | 0805 | Pull-down at IC6:22 in Schaltung 1. |
| R1 | 4.7 kOhm | 0805 |  |
| R607 | 1 MOhm | 0805 |  |
| R101 | 43 kOhm | 0805 | Schaltung 1 coupling / divider path. |
| R891 | 330 Ohm | 0805 |  |
| R37 | n.b. | 0805 | Not populated. |
| R41 | n.b. | 0805 | Not populated. |
| R42 | n.b. | 0805 | Not populated. |
| RA1 | 16B25K / 50K | resistor network | Exact network function not fully mapped. |

---

## Capacitors

### Known Values

| RefDes | Value / Status | Package / Type | Notes |
|---|---|---|---|
| C1 | 40 pF | 0805 C0G | Likely crystal load capacitor. |
| C2 | 40 pF | 0805 C0G | Likely crystal load capacitor. |
| C95 | 4.7 uF | Tantalum | Power / local reservoir capacitor. |

### General Rule for Previously Unknown Populated 0805 Capacitors

All previously unknown populated 0805 capacitors, except C1 and C2, are now treated as:

| Group | Value | Package |
|---|---:|---|
| Populated unknown 0805 capacitors except C1/C2 | 100 nF | 0805 |

This includes, unless otherwise noted:

| RefDes | Value | Package |
|---|---:|---|
| C35 | 100 nF | 0805 |
| C31 | 100 nF | 0805 |
| C34 | 100 nF | 0805 |
| C3 | 100 nF | 0805 |
| C23 | 100 nF | 0805 |
| C25 | 100 nF | 0805 |
| C14 | 100 nF | 0805 |
| C999 | 100 nF | 0805 |
| C13 | 100 nF | 0805 |
| C36 | 100 nF | 0805 |
| C9 | 100 nF | 0805 |
| C6 | 100 nF | 0805 |
| C10 | 100 nF | 0805 |
| C7 | 100 nF | 0805 |
| C22 | 100 nF | 0805 |
| C133 | 100 nF | 0805 |
| C131 | 100 nF | 0805 |
| C27 | 100 nF | 0805 |
| C16 | 100 nF | 0805 |
| C18 | 100 nF | 0805 |
| C19 | 100 nF | 0805 |
| C134 | 100 nF | 0805 |
| C135 | 100 nF | 0805 |
| C15 | 100 nF | 0805 |
| C080 | 100 nF | 0805 |
| C4 | 100 nF | 0805 |
| C20 | 100 nF | 0805 |
| C21 | 100 nF | 0805 |
| C5 | 100 nF | 0805 |
| C132 | 100 nF | 0805 |
| C137 | 100 nF | 0805 |

### Not Fitted / Not Populated Capacitors

| RefDes | Status |
|---|---|
| C17 | n.f. / not fitted |
| C989 | n.f. / not fitted |
| C990 | n.f. / not fitted |
| C991 | n.f. / not fitted |
| C992 | n.f. / not fitted |
| C995 | n.f. / not fitted |
| C996 | n.f. / not fitted |
| C997 | n.f. / not fitted |
| C998 | n.f. / not fitted |

---

## Diodes / SOT-23 Devices

| RefDes | Marking | Package | Notes |
|---|---|---|---|
| D133 | C3 | SOT-23-like | Unknown exact part. |
| D131 | C3 | SOT-23-like | Unknown exact part. |
| D132 | C3 | SOT-23-like | Unknown exact part. |
| D134 | C3 | SOT-23-like | Unknown exact part. |
| D135 | C3 | SOT-23-like | Unknown exact part. |
| D137 | C3 | SOT-23-like | Unknown exact part. |
| D95 | C3 | SOT-23-like | Unknown exact part. |

---

## Fuses / Links

| RefDes | Known Connection / Function | Notes |
|---|---|---|
| F1 | IC9:5 to IC7 clock inputs IC7:4/7/28 | IC7 timer clock feed. |
| F2 | IC20:3 to IC10:22 / IC11:22 / IC3:19 `/OE` net | Global read `/OE` link. |
| F3 | IC20:1 / R/W net to IC1:47 | R/W link. |
| F4 | IC20:2 / E net to IC1:48 | E / bus enable link. |

---

## Open / To Be Confirmed

| Topic | Current State | Next Useful Action |
|---|---|---|
| ROM start address | Likely `0x8000-0xFFFF` for 27C256 | Confirm using reset vector at EPROM offset `0x7FFE/0x7FFF`. |
| IC3 input bit semantics | Electrical sources known | Determine meaning of IC500/IC6 signals by tracing secondary board / firmware accesses. |
| F pin output | 330 ohm small low-side driver | Determine whether MIL, diagnostic, or small relay/logical output. |
| 3G / G distributor functions | Both ignition/distributor-related candidates | Verify exact distributor signal names by harness / oscilloscope. |
| IC20 Gate 3 / C10 node | Timing/reset/status candidate | Trace external C10 node and IC1:11 behavior. |
| IC6:22 / Schaltung 1 | IC6 comparator/status path into IC1:9 via IC20 Gate2 | Determine physical input source and firmware meaning. |
| SE074 logic | Injector pre-driver logic strongly suspected | Determine exact channel/bank/phase behavior between IC7 outputs, IC1 direct lines, IC700/IC701 and injector outputs. |
| External connector pinout | Working list exists | Continue verifying remaining A/B/2x/3x pins against harness and PCB topology. |

---

## Notes

- This BOM is a reverse-engineering working document, not an official Mazda or Denso BOM.
- Confidence varies per row. Connector/output mappings are based on continuity measurements, driver topology, and functional plausibility.
- Keep not-fitted (`n.f.` / `n.b.`) parts separate from populated 100 nF capacitors.
- The ECU appears to use separate logic/signal ground and low-side driver emitter / power-ground references in at least some regions.
- The common internet Probe/MX-6 pinouts are useful as signal-name references only; they do not directly match this KL05 ECU pin lettering.

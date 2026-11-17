# Firmware Findings From Related KL / KF Images

> Contributed notes, not measured on this board. They come from disassembling `firmware/ECU_079753-1815.bin` from this repository together with a set of related Denso images (KF11-C, KL01_C, KL07_C, KL31_F, KL55, KL59, KL85AZ). Each item says how it was established. Treat anything marked *inferred* as a hypothesis.

## 1. Answers to items in `open_questions.md`

| Open question | Finding | Basis |
|---|---|---|
| ROM start address | `0x8000-0xFFFF` is right. The reset vector at dump offset `0x7FFE` is `0x8E5E`, and all 23 vectors (`0xFFD2-0xFFFF`) point into `0x8E5E-0x9724`. | Read from the image. |
| MCU internal register area | The firmware addresses the MCU registers at **`0x1000-0x1079`**, not `0x0000-0x007F`. RAM starts at `0x0000` (cleared at reset from `0x0020`, used up to about `0x01FF`). The MC68HC711P2 datasheet map does not fit this part. | Every register access in the code uses `0x10xx`; direct-page addresses are used as ordinary variables. |
| MCU type | Instruction set is plain 68HC11: the image disassembles cleanly from all vectors. The part is a custom variant: registers run past the standard `0x103F`, and the vector table has two extra entries at `0xFFD2` and `0xFFD4`. | Disassembly. |
| External RAM | `0x2C00-0x2FFF` is tested at reset; the stack top is `0x2EFF`. | Reset code. |
| "F pin output, 330 ohm" | Pin 1F is the diagnostic connector's code-output terminal (FEN) in the factory terminal tables. In firmware the fault-code flashing routine toggles bit 1 of `0x1008`. | Mazda 626/MX-6 workshop manual terminal table; flashing routine. Pin-to-port link is *inferred*. |
| "3G / G distributor functions" | Factory tables: **1G** = ignition signal to the igniter (IGT, output). **3G** = distributor G signal, **3E** = NE1, **3H** = NE2: all three are inputs. A low-side driver on 3G would not match the manual; worth re-measuring. | Workshop manual terminal tables (626 1994, Xedos 6 1992). |
| 2G | Coolant temperature sensor for the fan (analog input) on cars that have it. Firmware reads it on analog-switch channel 5 and raises code 69 when out of range. | German 626 GE wiring book and 1993 Probe pin chart; range check in code. |
| 3D | Ground in the factory tables (3A, 3B, 3C, 3D are all grounds), not an airflow-sensor signal. Airflow signal is 2B. | Workshop manual terminal tables. |
| 3I / 3J naming | Factory tables: 3I = VRIS solenoid No.1, 3J = VRIS solenoid No.2 (the reverse of the working list here). | Workshop manual terminal tables. |
| 3O / 3P | Factory tables: 3O = EGR vent solenoid, 3P = EGR vacuum solenoid. | Workshop manual terminal tables. Note the German 626 GE wiring book prints them the other way round. |

## 2. Image identification and format

| Item | Value in `ECU_079753-1815.bin` |
|---|---|
| ID string (offset `0x0A`) | `KL05_C` |
| Build date (offsets 7-9, BCD yy mm dd) | 1992-03-31 |
| Checksum | Sum of all big-endian 16-bit words = `0xAA55`: valid |
| Reset vector | `0x8E5E` |

- **Checksum rule:** the 16-bit sum of all big-endian words over the whole image must be `0xAA55`. The routine only runs in a test mode, so a wrong sum does not by itself stop the ECU. The factory balances the sum with the word at `0xFFBE`; the word at offset 0 (`3F3F`) can be used instead.
- **Same program as KL01_C and KL07_C.** Against KL01_C (EPROM label 079753-1605) only 11 bytes differ:

| CPU address | KL01_C | KL05_C | Meaning |
|---|---|---|---|
| `0x800D` | `31` | `35` | ID digit |
| `0x804A-0x804E` | `07 0C 06 09 06` | `08 06 06 06 08` | calibration, not identified |
| `0x8D85-0x8D86` | `26 26` | `FF 14` | calibration, not identified |
| `0xFCE4-0xFCE5` | `00 00` | `01 F4` | 16-bit constant (500), not identified |
| `0xFFBE-0xFFBF` | `EE 2B` | `FA 63` | checksum balance word |

## 3. External peripherals as the firmware uses them

This agrees with the decoder tracing in `external_bus_memory.md`.

| Address | Use in code |
|---|---|
| `0x2000-0x2006` | Timer chip registers |
| `0x2400` bit 4 | Shared feedback input read by the output-circuit self-test |
| `0x2401` bit 2 | VRIS solenoid No.1 (0 = energised) |
| `0x2401` bit 3 | VRIS solenoid No.2 (0 = energised) |
| `0x2401` bit 4 | Pressure regulator control solenoid |
| `0x2401` bit 7 | Toggled continuously inside long loops (for example the ROM checksum loop): most likely a watchdog kick. This may be the "0x2401 Bit7" item in `ic4_pia.md`. |
| `0x2800` | Input buffer |

## 4. Outputs, from the output-circuit self-test

The self-test (jump table at `0xF947` in this image) switches one output per step, reads the shared feedback bit and raises that output's fault code on a mismatch. That ties each port bit to a named circuit.

| Step | Port bit | Fault code | Circuit |
|---|---|---|---|
| 1 | `0x2401` bit 4 | 25 | Pressure regulator control solenoid |
| 2 | `0x1033` bit 2 | 26 | Purge solenoid |
| 3 | `0x1033` bit 1 | 28 | EGR vacuum solenoid |
| 4 | `0x1033` bit 0 | 29 | EGR vent solenoid |
| 5 | `0x1000` bit 3 | 34 | Idle speed control valve |
| 6 | `0x2401` bit 2 | 41 | VRIS solenoid No.1 |
| 7 | `0x2401` bit 3 | 46 | VRIS solenoid No.2 |
| 8 | `0x1002` bit 4 | 67 | Cooling fan relay |

The firmware tests both VRIS outputs. If the 3I driver is unpopulated on this board, code 46 handling would be worth a look.

## 5. Analog inputs

Seven inputs are converted directly by the MCU (16-bit results at `0x1040-0x104C`, copied to RAM `0x92-0x9E`). One more goes through an 8-way analog switch (the TC4051): the select bits are the top three bits of `0x1033`, the result is read from `0x104E` into RAM `0xA3 + 2 x channel`. Each sensor was identified by the fault code its range check raises.

| Source | RAM (raw) | Sensor | Fault code |
|---|---|---|---|
| `0x1040` | `0x92` | Airflow meter | 08 |
| `0x1042` | `0x94` | Throttle sensor | 12 |
| switch ch. 1 | `0xA5` | Knock sensor level | 05 |
| switch ch. 2 | `0xA7` | Barometric pressure (sensor inside the ECU) | 14 |
| switch ch. 4 | `0xAB` | Coolant temperature | 09 |
| switch ch. 5 | `0xAD` | Coolant temperature for the fan | 69 |
| switch ch. 6 | `0xAF` | Intake air temperature | 10 |

Which connector pin feeds which channel is not visible in the firmware; the board tracing in this repository could close that.

## 6. Variables and scaling

| RAM | Meaning | Basis |
|---|---|---|
| `0xDE` (word) | Engine speed, 500/512 rpm per count | VRIS thresholds derived from it match the workshop manual |
| `0xBD` (word) | Load value from the airflow meter; row axis of the main map | Table lookup code |
| `0xD2` | Throttle position; `0xD6` = learned closed-throttle value | Range check for code 12 |
| `0x41` | Six rpm flags selecting the VRIS table column | VRIS routine |
| `0x7C, 0x7E, 0x82` | Current fault flags, merged into stored flags `0x15, 0x17, 0x19` | Fault-flag table |
| `0x80, 0x81` | Faults found by the output self-test | Self-test routine |

- **Calibration rpm thresholds** are single bytes, 31.25 rpm per bit, stored as (hysteresis, threshold) pairs.
- **Ignition scale:** degrees BTDC = (value - 43) x 0.234. *Inferred* from the spark-delay arithmetic and from the fixed test-mode value 86 giving 10 degrees, the base timing with the test connector grounded.

## 7. Tables located in this image

| CPU address | Size | Content |
|---|---|---|
| `0x8644`, `0x8718` | 15 x 14 | Main ignition map, two copies selected by a flag. Rows = load, columns = 500-7000 rpm in 500 rpm steps. |
| `0x83C0` | 13 x 14 | Load x rpm table, purpose not identified |
| `0x833C`, `0x837E` | 8 x 8 | Coolant-temperature tables, purpose not identified |
| `0x8AEE`, `0x8AFE` | 8 x 16-bit | Idle-speed targets against coolant temperature. Warm values 600 and 688 rpm. |
| `0x8D8D` | 3 x 7 bytes | VRIS strategy table: rows chosen by two throttle-opening thresholds (which row is which opening is not confirmed), columns by rpm. Bit `0x10` and bit `0x20` select the two solenoids. |
| `0x80BB...` | byte pairs | VRIS column thresholds: 500, 1875, 2687, 3250, 4250, 6250 rpm |
| `0xF7F3`, `0xF7A6` | | Fault-code number table and (mask, RAM address) flag table |
| `0x847D` | byte | Fixed timing in test mode: 86 = 10.1 degrees BTDC |

VRIS table in this image (same as KL01_C and KL07_C):

```
row 1 : 00 10 10 10 10 10 10
row 2 : 00 10 10 10 10 10 10
row 3 : 00 00 00 00 10 30 00
```

Fault codes this image can report: 01-05, 08-10, 12, 14-17, 23-26, 28, 29, 34, 41, 46, 67, 69.

## 8. Not found

- A rev limiter. Searched for rpm comparisons, crank-period comparisons and rpm switch points; none acts as an over-rev fuel cut. It may be inside the injection timing code, which is not decoded.
- How the three HD63B40 timer outputs map onto six injectors. The board work on SE074 here is the best lead.

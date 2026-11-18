# Firmware Findings From Related KL / KF Images

> Contributed notes, not measured on this board. They come from disassembling `firmware/ECU_079753-1815.bin` from this repository together with a set of related Denso images (KF11-C, KL01_C, KL07_C, KL31_F, KL55, KL59, KL85AZ). Each item says how it was established. Treat anything marked *inferred* as a hypothesis.

## 1. Answers to items in `open_questions.md`

| Open question | Finding | Basis |
|---|---|---|
| ROM start address | `0x8000-0xFFFF` is right. The reset vector at dump offset `0x7FFE` is `0x8E5E`, and all 23 vectors (`0xFFD2-0xFFFF`) point into `0x8E5E-0x9724`. | Read from the image. |
| MCU internal register area | The firmware addresses the MCU registers at **`0x1000-0x1079`**, not `0x0000-0x007F`. RAM starts at `0x0000` (cleared at reset from `0x0020`, used up to about `0x01FF`). The MC68HC711P2 datasheet map does not fit this part. | Every register access in the code uses `0x10xx`; direct-page addresses are used as ordinary variables. |
| MCU type | Instruction set is plain 68HC11: the image disassembles cleanly from all vectors. The part is a custom variant: registers run past the standard `0x103F`, and the vector table has two extra entries at `0xFFD2` and `0xFFD4`. | Disassembly. |
| External RAM | `0x2C00-0x2FFF` is cleared at reset; the stack top is `0x2EFF`. | Reset code. |
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

- **Checksum rule:** the 16-bit sum of all big-endian words over the whole image must be `0xAA55`. The routine only runs in a factory bench mode (section 9), so a wrong sum does not stop the ECU in the car. The factory balances the sum with the word at `0xFFBE`; the word at offset 0 (`3F3F`) can be used instead.
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
| `0x2000-0x2007` | Timer chip registers (16-bit accesses at `0x2002`, `0x2004`, `0x2006`: the three counters that time injectors, see section 8) |
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

The firmware tests both VRIS outputs: code 41 for solenoid No.1 and code 46 for No.2. Which connector pin is which is the open point (factory tables: 3I = No.1, 3J = No.2; the working list here has them reversed). If one driver is unpopulated on this board, expect code 41 if it is the No.1 circuit and code 46 if it is No.2.

## 5. Analog inputs

Seven inputs are converted directly by the MCU (16-bit results at `0x1040-0x104C`, copied to RAM `0x92-0x9E`). One more goes through an 8-way analog switch (the TC4051): the select bits are the top three bits of `0x1033`, the result is read from `0x104E` into RAM `0xA3 + 2 x channel`. Each sensor was identified by the fault code its range check raises.

| Source | RAM (raw) | Sensor | Fault code |
|---|---|---|---|
| `0x1040` | `0x92` | Airflow meter | 08 |
| `0x1042` | `0x94` | Throttle sensor | 12 |
| switch ch. 1 | `0xA5` | Knock sensor level | 05 |
| switch ch. 2 | `0xA7` | Barometric pressure (sensor inside the ECU) | 14 |
| switch ch. 3 | `0xA9` | Battery voltage | none |
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
- **Ignition scale:** degrees BTDC = (value - 43) x 0.234. The step is traced: spark time = falling crank edge + (329 - value) x T / 512, with T the time between two falling edges (120 crank degrees), so one count is 120/512 degrees. Where top dead centre sits (43) is *inferred* from the idle timing curve giving 10 degrees at idle.

## 7. Tables located in this image

| CPU address | Size | Content |
|---|---|---|
| `0x8644`, `0x8718` | 15 x 14 | Main ignition map, two copies selected by a flag. Rows = load, columns = 500-7000 rpm in 500 rpm steps. |
| `0x83C0` | 13 x 14 | Fuel enrichment at high load. Rows = load, columns = 500-7000 rpm. 32 = no enrichment, one count = 1/128 (0.78 %); the largest value in this image gives +46 %. Scale traced in the KF11-C image, where the same code adds the table value into the fuel correction sum. |
| `0x833C`, `0x837E` | 8 x 8 | Coolant-temperature tables, purpose not identified |
| `0x8B4E`, `0x8B56`, `0x8B5E` | 8 bytes each | Idle-speed targets against coolant temperature (-40 to 100 C in 20 C steps), 8 counts of the rpm word per bit. Cold to warm: 1797 ... 656 rpm for the first two, 1203 ... 625 rpm for the third. Two flags built from the idle switch and load inputs select the table. |
| `0x8AEE`, `0x8AFE` | 8 x 16-bit | Idle control tables against coolant temperature, purpose not identified. An earlier version of this document called them idle-speed targets; that was wrong. |
| `0x8D8D` | 3 x 7 bytes | VRIS strategy table: rows chosen by two throttle-opening thresholds (which row is which opening is not confirmed), columns by rpm. Bit `0x10` and bit `0x20` select the two solenoids. |
| `0x80BB...` | byte pairs | VRIS column thresholds: 500, 1875, 2687, 3250, 4250, 6250 rpm |
| `0xF7F3`, `0xF7A6` | | Fault-code number table and (mask, RAM address) flag table |
| `0x847D` | byte | Fixed timing value 86 = 10.1 degrees BTDC. It is selected by the calibration byte before it (`0x847C`), which is 0 in this image, so it is not used. An earlier version of this document called it the test-mode timing. |

VRIS table in this image (same as KL01_C and KL07_C):

```
row 1 : 00 10 10 10 10 10 10
row 2 : 00 10 10 10 10 10 10
row 3 : 00 00 00 00 10 30 00
```

Fault codes this image can report: 01-05, 08-10, 12, 14-17, 23-26, 28, 29, 34, 41, 46, 67, 69.

## 8. Rev limiter and injector drive

An earlier version of this document listed both as not found.

**Rev limiter: fuel cut at 7500 rpm, 5500 rpm on a very cold engine.**

- The routine at `0xAC57` copies a limit into RAM `0xFF`: the byte at `0x806F` (240 = 7500 rpm) normally, the byte at `0x806E` (176 = 5500 rpm) while the coolant flag for about -15 C is clear (threshold byte 40 at `0x80CD`; temperature scale: degrees C = value x 0.625 - 40).
- The rpm byte (31.25 rpm per bit) is compared with RAM `0xFF`; above it, the fuel mode byte is set to "cut" and the injection sequencer skips every injector.
- The limit goes through a RAM byte, so a search for a direct rpm comparison against calibration does not find it.
- Read from this image; the mode logic was traced in KF11-C, which has the same routine.

**Six injectors, six timed outputs: three on the MCU, three on the timer chip.**

| Driver routine | Timed by |
|---|---|
| `0xB84D` | MCU output compare `0x101C` |
| `0xB8B4` | timer chip counter `0x2002` |
| `0xB904` | MCU output compare `0x101A` |
| `0xB96B` | timer chip counter `0x2004` |
| `0xB9BB` | MCU output compare `0x1018` |
| `0xBA22` | timer chip counter `0x2006` |

- Injection is sequential: the crank interrupt advances a position counter (0-11) and a sequencer calls one driver at the matching position.
- In KF11-C the sequencer table fires the drivers 120 crank degrees apart in the order MCU `0x101C`, chip `0x2002`, MCU `0x101A`, chip `0x2004`, MCU `0x1018`, chip `0x2006`. The two cylinder banks get separate pulse widths: MCU-timed injectors one bank, chip-timed injectors the other.
- A pulse starts immediately and its end is programmed; if the injector is still open, the new width is added to the remaining time. Pulse width is in timer counts.
- So the three HD63B40 outputs time three of the injectors and three MCU timer outputs time the other three. That is a firmware reading; which driver is which cylinder, and how the SE074 combines the lines, still needs the board.

## 9. From a line-by-line reading of KF11-C

The whole KF11-C program (same program family, 2.0 V6) has since been read instruction by instruction. The items below bear on open questions in this repository. They were traced in KF11-C, not in this image; port addresses are the same in both. Pin names come from the 1992 factory terminal table and are *inferred* unless marked.

**Time base.** One MCU timer count = 2 us and the periodic interrupt = 4.096 ms. *Inferred* from the engine-speed arithmetic, a fixed read compensation in the crank interrupt and the fault-code flash timing. It fits the 8 MHz crystal documented here.

**IC3 input port `0x2800` (question "IC3 bit semantics").** Each bit is accepted after two equal samples. Bits 2-5 are inverted in software (1 = pin low).

| Bit | Meaning | Factory pin | Confidence |
|---|---|---|---|
| 0 | Stop lamp switch | 1Q | guess |
| 1 | Headlamp switch | 1H | guess |
| 2 | Neutral/clutch switch (manual) or load signal from the automatic's control unit; low = driveline free | 1R | inferred |
| 3 | Manual/automatic identification; low = manual | 1U | inferred |
| 4 | Electrical load (blower or rear window) | 1P or 1J | guess |
| 5 | A/C related input (A/C request, or another load; `0x2401` bit 6 read as an input is a second A/C related line) | 1O ? | guess |
| 6 | Electrical load (rear window or blower) | 1J or 1P | guess |
| 7 | Power steering pressure switch | 1N | guess |

**Other inputs.**

| Port bit | Meaning | Factory pin |
|---|---|---|
| `0x2400` bit 0 | Starter signal | 1C |
| `0x2400` bit 1 | Idle switch, 1 = throttle closed | 1T |
| `0x2400` bits 2, 3 | "Reduce torque" signals from the automatic's control unit | 1S, 1V |
| `0x2400` bit 4 | Shared feedback line of the output self-test | |
| `0x2400` bits 5-7 | Variant code (board jumpers), read once at start | |
| `0x1031` bit 3 | Test connector TEN, low = grounded | 1I |
| `0x1000` bit 1 | Distributor G signal, sampled at each crank edge | 3G |

**Outputs not in section 4.**

| Port bit | Meaning |
|---|---|
| `0x1000` bit 7 | Ignition signal to the igniter (low = coil charging, rising edge = spark) |
| `0x1000` bit 3 | Idle speed control valve, 244 Hz (period 2048 counts) |
| `0x1002` bit 7 | Selects which crank signal (NE1 or NE2) feeds the crank capture input: the engine starts on NE1 and changes to NE2 above 500 rpm once NE2 is verified (*inferred* from which fault code a missing signal raises) |
| `0x1002` bits 5, 6 | Two further fan outputs beside bit 4 |
| `0x2401` bit 0 | A/C relay, low = on |
| `0x2401` bit 5 | Status line to the automatic's control unit (*guess*) |
| `0x1008` bit 1 | Fault code output (FEN, pin 1F) and warning lamp |
| `0x106E` | Monitor output (MEN, pin 1D): oxygen sensor state, switch monitor with TEN grounded |

**Knock circuit.** Knock is not measured by the analog converter. The firmware reads a hardware pulse counter (`0x1075`) twice per firing: once for the window after the spark and once for the background. It sends one byte over the serial port (`0x102A`, strobe on `0x1008` bit 5) that sets the sensitivity from an adaptive background level. A serial byte with a strobe fits the 74HC595 shift register on this board driving a threshold; that link is a *guess* the board tracing could confirm. The analog knock channel (switch channel 1) is used only for the sensor range check (code 05).

**Analog switch.** Channel 3 is battery voltage (used for injector dead time and coil dwell). Channel 7 is stored into the same variable.

**Checksum.** The ROM checksum routine runs only in a factory bench mode (TEN grounded, starter signal, a simulated engine speed of 8000 rpm or more), never in the car.

**Kept memory.** RAM `0x00-0x1D` holds learned values and stored fault codes as (complement, value) pairs with a signature word. It is checked on every main-loop pass; if the check fails, learned fuel trim, learned idle value and stored codes are reset together.

# Board Overview

## Project goal

The goal is to reverse engineer the ECU hardware well enough to build a reliable firmware and I/O map. The current board appears to use a main MCU/custom CPU with an external 8-bit data bus, external EPROM, external SRAM, peripheral ICs, address decoding logic and custom Denso driver devices.

## High-level architecture

```text
                 +----------------------+
                 | IC1 SC402617FN       |
                 | Main MCU / CPU       |
                 +----------+-----------+
                            |
        Address bus Adr0..Adr14, data bus IO0..IO7
                            |
        +-------------------+--------------------+
        |                   |                    |
+-------+------+    +-------+------+     +-------+------+
| IC11 27C256  |    | IC10 TC5564  |     | IC3 74HC541 |
| EPROM 32 KiB |    | SRAM 8 KiB   |     | Input buffer |
+--------------+    +--------------+     +--------------+
        |                   |                    |
        +-------------------+--------------------+
                            |
                   +--------+--------+
                   | Glue logic      |
                   | IC17, IC20 etc. |
                   +--------+--------+
                            |
        +-------------------+--------------------+
        |                   |                    |
+-------+------+    +-------+------+     +-------+------+
| IC4 HD63BP21 |    | IC7 HD63B40  |     | IC5/IC800   |
| PIA          |    | Peripheral   |     | SE123       |
+--------------+    +--------------+     +--------------+
```

## Known clocking

- `X1` is an 8 MHz crystal.
- `IC7 pin 17` is `E` / system clock.
- The same `E`-like signal is shared with `IC4 pin 25`, `IC10 pin 26` and through `F4` to `IC1 pin 48`.

## Memory devices

- `IC11` is a `27C256`, 32 KiB EPROM.
- `IC10` is a `TC5564APL`, 8 KiB SRAM.
- ROM and RAM share the lower address bus and data bus.

## Peripheral and I/O devices

- `IC4 HD63BP21P` provides parallel I/O and interrupt/control pins.
- `IC7 HD63B40P` is connected to bus/control signals and likely participates in timing or peripheral functions.
- `IC3 74HC541` buffers external or secondary-board signals onto the data bus.
- `IC5` and `IC800` marked `SE123` are likely custom multi-channel driver devices.
- `IC6` and `IC500` appear to be related custom Denso devices.

## Current working assumptions

1. `IC1` is the main bus master.
2. `IC11`, `IC10`, `IC3`, `IC4` and `IC7` are bus-accessible devices or closely tied to bus cycles.
3. `IC17 74HC138` generates multiple active-low chip select lines.
4. `IC20 74HC00` combines `R/W` and `E` or bus-enable style signals to generate a global read output-enable signal.
5. `SE123` devices are not simple logic ICs. Their pinout and 12 V pin suggest output driver behavior.

## Repository purpose

This repository is meant to preserve findings in a structured way so that future analysis does not repeatedly rediscover the same mistakes, because apparently silicon enjoys being cryptic and humans enjoy tracing it twice.

# Open Questions and Next Steps

## Highest-priority hardware questions

1. What exactly is `IC1 SC402617FN`?
   - Main MCU is likely, but the exact CPU core and memory map model are still unknown.

2. What are all `IC17 74HC138` inputs and enables?
   - This is essential for decoding chip selects.

3. What are all `IC17` output destinations?
   - Known partial outputs exist, but the full decoder map is needed.

4. What is the exact behavior of `IC20` gates 3 and 4?
   - Gate 1 and gate 2 are partly understood.
   - Gate 4 likely inverts an `IC17` output into EPROM enable behavior.

5. What is the true function of the `SE123` devices?
   - Output driver is likely, but pin function and electrical topology need proof.

6. What is `IC6 151821-0020`?
   - Its relation to `IC500` and the `IC3` input buffer is important.

## Suggested tracing tasks

### IC17 complete mapping

Trace:

- Inputs `A`, `B`, `C`
- Enables `G1`, `/G2A`, `/G2B`
- Outputs `/Y0..Y7`
- Each output destination

Create a truth table once input sources are known.

### ROM/RAM control validation

Measure or trace:

- `IC11 /CE`
- `IC11 /OE`
- `IC10 CE1/CE2` depending on exact pin names
- `IC10 /OE`
- `IC10 R/W`
- `IC20 pin 3`
- `IC20 pin 11`

### Bus timing capture

Use a logic analyzer on:

- `E` / bus-enable net
- `R/W` net
- Global `/OE`
- At least some address lines
- At least some data lines
- One chip select line at a time

## Suggested firmware tasks

1. Identify all direct accesses to addresses in `0x2000..0x2FFF`.
2. Identify repeated bit-test operations such as `BRSET` / `BRCLR` around suspected peripheral addresses.
3. Create provisional labels for hardware-backed locations.
4. Compare firmware access patterns against IC17 chip select hypotheses.
5. Use hardware traces to confirm address ranges.

## Suggested repository structure

```text
KLDE_ECU_Reverse/
├── README.md
├── docs/
│   ├── board_overview.md
│   ├── ic_inventory.md
│   ├── ic1_pin_mapping.md
│   ├── external_bus_memory.md
│   ├── chip_select_glue_logic.md
│   ├── peripherals.md
│   ├── se123_driver_devices.md
│   ├── connectors_and_signals.md
│   ├── firmware_analysis.md
│   └── open_questions.md
├── hardware/
│   ├── photos/
│   ├── schematics_partial/
│   └── netlists/
├── firmware/
│   ├── dumps/
│   ├── ghidra_notes/
│   └── labels/
├── captures/
│   ├── logic_analyzer/
│   └── oscilloscope/
└── tools/
    ├── sigrok/
    └── python/
```

## Suggested commit order

1. Add documentation skeleton.
2. Add raw photos and board reference images.
3. Add ROM dump and checksum metadata, if legally and practically okay for private use.
4. Add partial netlists.
5. Add Ghidra labels and comments separately from raw firmware dumps.
6. Add scripts only after they have a clear purpose.

## Documentation rule

Every finding should ideally include:

- Source: continuity test, datasheet, visual inspection, logic analyzer, firmware reference.
- Confidence: verified, likely, hypothesis.
- Date or revision.
- Any contradiction or earlier wrong assumption.

Reverse engineering is mostly archaeology, except the pottery has 80 pins and lies to you.

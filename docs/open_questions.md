# Open Questions and Next Targets

## CPU / firmware model

| Question | Why it matters | Suggested next step |
|---|---|---|
| Is `IC1 = SC402617FN` truly HC11-compatible or only HC11-like? | Determines disassembly accuracy | Compare reset vectors, opcodes, interrupt behavior, and bus timing |
| What is the correct ROM base address? | Required for valid code references | Derive from reset vector and chip-select logic |
| Are any ROM regions mirrored? | Prevents false xrefs and wrong labels | Check decode width and address-line use |

## Memory map

| Question | Suggested next step |
|---|---|
| Exact RAM address range? | Probe `IC10` chip-select and correlate firmware RAM references |
| Exact PIA/PTM register ranges? | Trace `IC17` outputs and firmware access addresses |
| Does `IC3` occupy a single address or range? | Capture `IC17:13` and `/OE` during reads |
| Which addresses are write-only outputs? | Correlate writes with SE123 and 74HC595 activity |

## IC17 / IC20 logic

| Question | Suggested next step |
|---|---|
| How exactly do `IC20:12/13`, `IC20:11`, `IC17:4`, `R56`, and any `IC1:14` relation fit together? | Re-probe and redraw that gate with correct 74HC00 pinout |
| Does `IC20:3` match read-cycle `/OE` timing? | Capture `R/W`, `E`, and `/OE` with logic analyzer |
| Is `IC20:6` really connected to `IC1:19`, and how does this coexist with `A10`? | Check for net-name collision or tracing error |

## IC6 / IC500 level shifter

| Question | Suggested next step |
|---|---|
| Are outputs open collector/open drain or push-pull? | Measure with pull-up/pull-down and current limiting |
| What does `IC6:22` sense in the ECU? | Trace input path and compare with firmware reads |
| Which external signals feed `IC500`? | Trace ribbon connector and connector pins |
| Which channels are ground-detect vs +12 V-detect in this ECU? | Bench-test each channel safely |

## SE123 output drivers

| Question | Suggested next step |
|---|---|
| Are SE123 outputs low-side sinks, high-side drivers, or open-collector outputs? | Dummy-load test with current-limited supply |
| Does the logic-side-to-output-side pin mirror hold for every channel? | Toggle known control pins and measure output pins |
| What vehicle functions correspond to `A1`, `A9`, `A10`, `B12`, `C4`, `C11`, `C12`, `C19`, `C20`, `D12..D19`? | Use wiring diagrams and connector probing |

## Ghidra / firmware

| Question | Suggested next step |
|---|---|
| What does `0x2401 bit 7` control or report? | Track all xrefs and probe `IC4` pins during state changes |
| What are `DAT_0049`, `DAT_0192`, `DAT_019B`, and `MEAS_ENABLE_FLAGS_67`? | Track writes, initialization, and branch-dependent behavior |
| Which routines write to output-driver control pins? | Find store instructions into decoded I/O ranges |
| Which routines read external inputs through `IC3`? | Find reads from `IC3` selected address range |

## Documentation TODO

- Add exact HC11 Ghidra processor module repository name.
- Add ROM checksum / dump hash.
- Add photos or annotated board images.
- Add connector pin table with direction and expected voltage.
- Add verified memory map table.
- Add firmware label export from Ghidra.

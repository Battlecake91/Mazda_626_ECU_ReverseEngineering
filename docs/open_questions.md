# Open Questions

## MCU / CPU Identity

- What exactly is `IC1 = SC402617FN`?
- Is it a Denso-custom HC11 derivative, a masked Motorola variant, or another compatible MCU?
- How exactly do the internal RAM / EEPROM / register ranges map on this part?
- How should the `0xBE40-0xBFFF` boot ROM / vector area from reference material be represented in Ghidra for this ECU?

## Ghidra HC11 Library

- Add the exact third-party Ghidra HC11 processor / language module repository or release name.
- Verify instruction decoding against known reset-vector startup code.

## IC20 Glue Logic

- Re-verify `IC20:6 -> IC1:19` because `IC1:19` is also listed as `A10`.
- Determine the exact role of `IC20` gate 3 around `pins 8/9/10`, `R606`, and `C10`.
- Confirm the exact net relationship between `IC20:11`, `IC11:20`, and `IC17:4` after correcting NAND gate pin direction.

## IC4 PIA

- Confirm full pin-to-port mapping using the `HD63BP21P` datasheet.
- Verify population status of `R37`, `R41`, and `R42`, because older BOM notes and current port pull-up/down notes conflict.
- Determine the external function of connector `A20` connected to `IC4:17`.
- Confirm how firmware configures Port A / Port B DDR and control registers.

## IC7 Timer

- Measure `IC9:11`, `IC9:9`, and `IC9:5` to determine actual timer clock frequency.
- Verify pull-up refdes for `C14` and `C15` paths (`R85` / `R86` naming conflict).
- Identify all firmware writes to timer registers.
- Determine whether timer channels directly generate injector pulse widths.

## SE074 Injector Driver Hypothesis

- Confirm `IC700` and `IC701` pin numbering / orientation, especially because supply pins appear different between devices.
- Determine why `IC701` receives timer outputs but `IC700` receives direct MCU lines.
- Verify injector output mapping using continuity to ECU connector and harness.
- Resolve whether internal Denso transistor numbering or US wiring diagram injector numbering is correct.
- Determine function of shared `IC700/IC701 pins 1+2` line and `IC250:19`.
- Determine function of `IC701:10 -> R825 -> T801` diagnostic / sense path.

## SE123 Devices

- Confirm whether `SE123` pins `14-22` are outputs, inputs, or protected driver pins.
- Verify channel mirror hypothesis by tracing each logic-side pin to external-side behavior.
- Determine whether `C4` shared routing between `IC5`, `IC800`, and SE074 context is supply, enable, diagnostic, or output-related.

## D151821-0020 Level Shifters

- Verify all `IC6` and `IC500` input/output channel polarities on the bench.
- Determine exact reference behavior of comparator pins `22/23` and output pin `2`.
- Map `IC3_INPUT_PORT` bits to real external vehicle signals.

## Analog Front-End

- Trace `IC13` TC4051 channel inputs and output.
- Trace `IC21` TLC272 amplifier stages.
- Identify sensor inputs and scaling networks.

## Capacitors / BOM

- Reconcile connector names such as `C14`, `C15`, `C16`, `C17` with capacitor refdes of same names. This naming collision is a little trap with teeth.
- Verify all n.f. / n.b. designations before final BOM export.
- Determine exact values for any parts not covered by the new capacitor and resistor value notes.

## Bus Capture

- Capture external bus cycles with DSLogic Plus.
- Decode address/data/RW/E/chip-select timing.
- Build a Python watcher for RAM and memory-mapped I/O accesses.
- Confirm external address map with real bus captures instead of static tracing only.

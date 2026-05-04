# Firmware Analysis Notes

## Current tool context

Firmware analysis is being performed in Ghidra. Hardware findings should be used to improve memory maps, peripheral labels and variable interpretation.

## Known code reference around 0x998E

A reset/service routine around address `0x998E` accesses `0x2401`:

```asm
RESET_SERVICE_998E:
998e  ce 24 01      LDX   #0x2401
9991  1e 00 80 08   BRSET 0x0,IX => DAT_2401,0x80,LAB_999d
9995  ce 24 01      LDX   #0x2401
```

Important note:

- `RAM_EXT` was already assigned to `0x2000..0x2FFF`.
- `0x2401` falls inside that range.
- This may be real RAM, memory-mapped peripheral space, mirrored space, or a misidentified region.

Do not blindly treat every access in `0x2000..0x2FFF` as normal RAM until the address decode is proven. That is exactly how one earns a haunted Ghidra project.

## Existing label note

- `SUB_99AC` is already named `ADC_PHASE_FLAGS_59`.

## Hardware hints relevant to firmware mapping

### Motorola-style bus hints

The presence of:

- `R/W`
- `E` / system clock
- `HD63BP21P`
- `HD63B40P`

suggests a Motorola-like bus timing model. This may help when interpreting firmware architecture and peripheral behavior.

### Devices likely visible on the data bus

The following devices are most likely connected to the external data bus or externally readable/writable bus space:

| Device | Reason |
|---|---|
| IC11 27C256 EPROM | Program/data ROM on data bus |
| IC10 TC5564 SRAM | External RAM on data bus |
| IC3 74HC541 | Outputs connected to D0-D7 and gated by address decode plus `/OE` |
| IC4 HD63BP21P | Bus-style peripheral with R/W and E/control lines |
| IC7 HD63B40P | Bus-style peripheral with E/system clock and R/W/control lines |
| Possibly IC8/IC9 logic paths | Could expose latched output or status behavior indirectly |

## Suggested Ghidra workflow

1. Keep raw hardware names in labels first, for example `PORT_IC4_PIA_B`, `REG_IC7_UNKNOWN_00`, `INPUT_IC3_BUFFER`.
2. Create a separate note for inferred semantic names, for example `ADC_PHASE_FLAGS`.
3. Mark all guessed addresses with a confidence tag in comments.
4. When a hardware address is confirmed by chip select tracing, rename it from unknown to a stable label.
5. Keep `RAM_EXT` conservative until the full decode is known.

## Logic analyzer watcher idea

A DSLogic Plus with `sigrok-cli` and Python can be used to build a watcher for external RAM or I/O access patterns.

Possible capture targets:

- Address lines needed for target region detection.
- Data bus `IO0..IO7`.
- `R/W` net.
- `E` / bus-enable net.
- Global `/OE` from `IC20 pin 3`.
- Specific chip select lines from `IC17`.

The practical challenge is channel count. Full address + full data + control lines requires many channels. For early work, capture a subset:

- `E`
- `R/W`
- selected high address lines
- one or two chip select lines
- data bus if enough channels are available

## Open firmware questions

- What CPU core or instruction set exactly matches `SC402617FN`?
- Is the external EPROM directly executable code, lookup data, calibration data, or a mixture?
- Where exactly are reset vectors located?
- Which addresses select IC3, IC4, IC7, RAM and ROM?
- Is `0x2401` RAM, I/O, or a mirrored/register-like location?

# Peripheral Summary

## Confirmed External Peripherals

| Device | Address range | Select source | Notes |
|---|---:|---|---|
| `IC7` HD63B40P timer | `0x2000-0x23FF` | `IC17:15 (/Y0)` | Three output channels routed to connector `C14-C16` and `SE074` circuitry. |
| `IC4` HD63BP21P PIA | `0x2400-0x27FF` | `IC17:14 (/Y1)` | Register select wiring mirrored: `RS1=A0`, `RS0=A1`. |
| `IC3` 74HC541 input port | `0x2800-0x2BFF` | `IC17:13 (/Y2)` plus global `/OE` | Reads level-shifter outputs from `IC500` and `IC6`. |
| `IC10` TC5564 SRAM | `0x2C00-0x2FFF` working window | Decoder / bus logic | External SRAM. |
| `IC11` 27C256 ROM | `0x8000-0xFFFF` | ROM select / address decode | External program ROM and vectors. |

## Analog / Mixed Signal

| Device | Type | Current notes |
|---|---|---|
| `IC13` TC4051BP | 8-channel analog mux | Full channel routing pending. |
| `IC21` TLC272 | Dual op amp | Analog conditioning. |
| `IC6` / `IC500` D151821-0020 | Comparator / level shifter | Converts / conditions external 12 V, ground-detect, and comparator signals to 5 V logic. |

## Output / Driver ICs

| Device | Current role |
|---|---|
| `IC5` SE123 | Denso custom multi-channel output driver / interface IC. |
| `IC800` SE123 | Second Denso custom multi-channel output driver / interface IC. |
| `IC700` SE074 | Likely 3-channel injector pre-driver / driver logic. |
| `IC701` SE074 | Likely 3-channel injector pre-driver / driver logic. |

## Timer / Injector Path Summary

The current strong hypothesis is:

```text
IC1 firmware configures IC7 timer
IC7 O1/O2/O3 generate timed pulse signals
IC701 SE074 receives those three timer outputs
IC701 drives three injector transistor stages
IC700 receives related control lines directly from IC1
IC700 drives the other three injector transistor stages
```

This creates a plausible 6-cylinder injector arrangement using two 3-channel SE074 devices.

The unresolved architectural question is why `IC700` is driven by direct `IC1` lines while `IC701` receives the dedicated `IC7` timer outputs. Possible explanations include:

- V6 bank split,
- staged / paired injection logic,
- duplicated pulse generation in MCU firmware,
- SE074-internal bank / channel / phase logic,
- missing intermediate routing not yet traced.

## Reset

`IC4 /RESET` and `IC7 /RESET` are tied together and also routed externally via `D7`. On the daughterboard, `D7` reaches `IC001:4` (`SE134`).

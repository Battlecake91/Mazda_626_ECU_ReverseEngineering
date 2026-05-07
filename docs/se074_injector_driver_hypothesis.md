# SE074 Injector Driver Hypothesis

## Summary

`IC700` and `IC701`, both marked `SE074`, are currently believed to be Denso 3-channel injector pre-driver / driver-logic ICs.

This hypothesis is based on:

- three control inputs per device,
- three output pins per device going through resistors to transistor stages,
- transistor reference designators `T711`, `T721`, `T731`, `T741`, `T751`, `T761`,
- external connector pins associated with six injector outputs,
- timer outputs from `IC7` feeding `IC701`.

## Supply Pins

| Device | +5 V | GND | +12 V |
|---|---|---|---|
| `IC700 SE074` | Pin 16 | Pins 3, 8, 14 | Pin 9 |
| `IC701 SE074` | Pins 3, 14, 16 | Pin 8 | Pin 9 |

The supply mapping differs between the two devices, or at least the measured pins do. This should be carefully verified against board orientation and package pin numbering before final schematic capture. Naturally, the custom ICs chose violence.

## Shared Pins 1/2

| Signal | Connection |
|---|---|
| `IC701:1+2` | Connected together with `IC700:1+2`, external `C3` path, and `IC250:19`. |
| External `C3` | Goes nowhere / open externally according to current tracing. |

Current interpretation:

- Shared enable / diagnostic / sense / mode line candidate.
- `IC250 MP611` pin 19 may supervise or condition this line.

## IC701 Input Side

`IC701` receives three timer outputs from `IC7`:

| IC701 pin | Source | Notes |
|---:|---|---|
| 4 | External `C14` -> `IC7:27 / O1` | Pull-up currently noted as `R85`. |
| 5 | External `C15` -> `IC7:3 / O2` | Pull-up currently noted as `R86`. |
| 6 | External `C16` -> `IC7:6 / O3` | Pull-up `R87`. |
| 7 | External `C17` -> `IC1:6` | Pull-up `R7`; possible enable / bank / mode. |
| 10 | `R825 -> T801:1` | See diagnostic / ground-monitor path below. |

## IC701 Output Side

| IC701 pin | Route | External pin | Wiring-plan injector note |
|---:|---|---|---|
| 11 | `R763 -> T761` | `3Z` | Injector 6 according to current note. |
| 12 | `R743 -> T741` | `3X` | Injector 2 according to plan, questionable. |
| 13 | `R723 -> T721` | `3V` | Injector 3 according to plan, questionable / incomplete note. |

## IC700 Input Side

`IC700` receives related control signals directly from `IC1`, not directly from the `IC7` timer outputs:

| IC700 pin | Source | Notes |
|---:|---|---|
| 4 | External `C5` -> `IC1:5` | Pull-up `R8`. |
| 5 | External `C6` -> `IC1:4` | Pull-up `R9`. |
| 6 | External `D6` -> `IC1:3` | Pull-up `R10`. |
| 7 | External `C8` -> `IC1:26` | Pull-up `R18`; possible enable / bank / mode. |

## IC700 Output Side

| IC700 pin | Route | External pin | Wiring-plan injector note |
|---:|---|---|---|
| 11 | `R753 -> T751` | `3Y` | Injector 4 according to plan, questionable. |
| 12 | `R733 -> T731` | `3W` | Injector 5 according to plan, questionable. |
| 13 | `R713 -> T711` | `3U` | Injector 1 according to plan. |

## Diagnostic / Sense / Enable Candidate Around T801

| Node | Connection |
|---|---|
| `IC701:10` | `R825 -> T801:1`. |
| `T801:2` | External GND pins `3C/3D`. |
| `T801:3` | Pull-up to VCC and voltage divider / tantalum capacitor network. |

Current interpretation:

- Diagnostic / sense / fail-safe / ground-monitor candidate.
- Could be related to injector driver enable or fault detection.

## Internal Numbering vs US Wiring Diagram

The internal Denso transistor numbering suggests a systematic six-channel order:

| Transistor | Possible injector number by internal numbering |
|---|---:|
| `T711` | Injector 1 |
| `T721` | Injector 2 |
| `T731` | Injector 3 |
| `T741` | Injector 4 |
| `T751` | Injector 5 |
| `T761` | Injector 6 |

This leads to the strong working hypothesis:

- `IC700` drives injectors 1 / 3 / 5 or one bank group depending on final mapping.
- `IC701` drives injectors 2 / 4 / 6 or the other bank group depending on final mapping.

The US wiring diagram injector mapping is currently treated as questionable and should not override measured board topology.

## Main Open Question

Why does `IC701` receive `IC7` timer outputs while `IC700` receives direct `IC1` lines?

Possible explanations:

1. `IC1` generates pulse signals for the second bank itself.
2. `IC7` only drives one bank and MCU pins drive the other bank.
3. `SE074` contains internal channel / bank / phase logic.
4. The direct `IC1` lines are not actual pulse inputs but enables / masks / selects.
5. There is still missing routing through the daughterboard.

This is a prime target for powered signal tracing during cranking / simulated RPM.

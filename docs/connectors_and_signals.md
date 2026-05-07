# Connectors and External Signals

## Naming Convention

Connector references use a letter plus pin number, for example:

- `A5` = connector row / side `A`, pin 5.
- `B7` = connector row / side `B`, pin 7.
- External daughterboard outputs also use forms such as `3U`, `3V`, `3W`, etc.

## Main Board to Daughterboard / External Signal Examples

| Signal | Route / meaning |
|---|---|
| `A1` | Related to `IC5:21`, has transistor marked `23` in SOT23-like package. |
| `A4` | `IC3 A1` input path through `IC500:7`. |
| `A5` | `IC3 A0` input path through `IC500:3`. |
| `A6` | `IC3 A6` input path through `IC500:10`. |
| `A9` | `IC800:18`. |
| `A10` | `IC5:19`. |
| `A20` | `IC4:17` via `R30` pull-up; likely `0x2401 bit 7` input candidate. |
| `B5` | `IC3 A2` input path through `IC500:8`. |
| `B6` | `IC3 A3` input path through `IC500:4`. |
| `B7` | `IC3 A5` input path through `IC500:6`. |
| `B8` | `IC4:16`. |
| `B9` | `IC3 A7` input path through `IC500:11`. |
| `B12` | `IC5:20`. |
| `C3` | Connected to `IC700:1+2`, `IC701:1+2`, `IC250:19`; externally goes nowhere according to current tracing. |
| `C4` | `IC5:14`, `IC800:14`, also context with `SE074:15`. |
| `C5` | `IC700:4 -> IC1:5`, pull-up `R8`. |
| `C6` | `IC700:5 -> IC1:4`, pull-up `R9`. |
| `C8` | `IC700:7 -> IC1:26`, pull-up `R18`. |
| `C11` | `IC5:15`, unpopulated resistor path to transistor, external connector. |
| `C12` | `IC5:16`, 4.3 k resistor + transistor, external connector. |
| `C14` | `IC7:27/O1 -> IC701:4`, pull-up `R85` current note. |
| `C15` | `IC7:3/O2 -> IC701:5`, pull-up `R86` current note. |
| `C16` | `IC7:6/O3 -> IC701:6`, pull-up `R87`. |
| `C17` | `IC701:7 -> IC1:6`, pull-up `R7`. |
| `C19` | `IC800:22`. |
| `C20` | `IC800:19`. |
| `D6` | `IC700:6 -> IC1:3`, pull-up `R10`. |
| `D7` | Common reset net: `IC4:34`, `IC7:8`, external board `IC001:4`. |
| `D12` | `IC800:15`. |
| `D13` | `IC800:20`. |
| `D14` | `IC5:18`. |
| `D15` | `IC800:21`. |
| `D16` | `IC5:17`. |
| `D18` | `IC800:17`. |
| `D19` | `IC800:16`. |

## Injector Output Connector Group

| External pin | Driver path | Current injector note |
|---|---|---|
| `3U` | `IC700:13 -> R713 -> T711` | Injector 1. |
| `3V` | `IC701:13 -> R723 -> T721` | Injector 3 per current plan note, but questionable. |
| `3W` | `IC700:12 -> R733 -> T731` | Injector 5 per plan note, questionable. |
| `3X` | `IC701:12 -> R743 -> T741` | Injector 2 per plan note, questionable. |
| `3Y` | `IC700:11 -> R753 -> T751` | Injector 4 per plan note, questionable. |
| `3Z` | `IC701:11 -> R763 -> T761` | Injector 6. |
| `3C/3D` | External ground | Used by `T801:2` ground-monitor / diagnostic path. |

## Notes

The internal transistor numbering `T711` to `T761` may be more reliable for injector ordering than the available US wiring diagram. Treat wiring-plan injector numbers as provisional until verified at the connector / harness level.

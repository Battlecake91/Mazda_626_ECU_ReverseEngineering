# IC4 HD63BP21P PIA

## Device

`IC4` is an `HD63BP21P` parallel interface adapter.

Working address range: `0x2400-0x27FF`.

## Register Select Wiring

Important correction: the register-select wiring is mirrored compared with the earlier assumption.

| IC4 pin | PIA signal | Connected address line |
|---:|---|---|
| 35 | `RS1` | `A0` |
| 36 | `RS0` | `A1` |

Therefore the effective register order is:

| Address | Effective `RS1:RS0` | Current interpretation |
|---:|---:|---|
| `0x2400` | `00` | Port A / DDRA depending on CRA bit 2. |
| `0x2401` | `10` | Port B / DDRB depending on CRB bit 2. |
| `0x2402` | `01` | CRA. |
| `0x2403` | `11` | CRB. |

## Bus and Select Pins

| IC4 pin | Signal / connection | Notes |
|---:|---|---|
| 21 | `R/W` net | Also `IC10:27`, `IC7:13`, `IC20:1`, through `F3` to `IC1:47`. |
| 23 | `CS` / select context | Connected to `IC17:14`, corresponding to address range `0x2400-0x27FF`. |
| 25 | `E` / enable context | Also on bus enable net with `IC10:26`, `IC7:17`, `IC9:11`, `IC20:2`, through `F4` to `IC1:48`. |
| 34 | `/RESET` | Common reset net with `IC7:8` and external `D7`. |
| 37, 38 | n.c. | Not connected. |
| 40 | `CA1` | Hard tied to GND. |

## Known Port / Control Pin Connections

The following list is currently the best known `IC4` pin mapping. Interpret against the `HD63BP21P` datasheet when assigning PA/PB/CA/CB functions.

| IC4 pin | Connection |
|---:|---|
| 7 | `R39` pull-up + `R42` pull-down. |
| 8 | `R38` pull-up + `R41` pull-down. |
| 9 | `R37` pull-up + `R40` pull-down. |
| 10 | `R36` pull-up + `IC5:6`. |
| 11 | `R35` pull-up + `IC5:10`. |
| 12 | `R34` pull-up + `IC5:8`. |
| 13 | `R33` pull-up + `IC5:9`. |
| 14 | `R32` pull-up + `IC5:7`. |
| 15 | `R31` pull-up + `IC800:7`. |
| 16 | External `B8`. |
| 17 | `R30` pull-up + external `A20`. |
| 18 | GND. |
| 19 | `R28` pull-down. |
| 22 | `R26` pull-down. |
| 24 | `R27` pull-down. |
| 29 | `R25` pull-down. |

## `0x2401 Bit 7` Finding

Earlier analysis considered `0x2401 bit 7` as possibly `CRA bit 7 / CA1 interrupt flag`. That is now considered incorrect.

Because `IC4 RS1/RS0` wiring is mirrored:

- `0x2401` corresponds to Port B / DDRB space, not CRA.
- Bit 7 likely maps to the physical port bit on `IC4 pin17`.
- `IC4 pin17` is connected to `R30` pull-up and external connector `A20`.
- `IC4 pin40 CA1` is tied to GND and should not produce an external CA1 event in normal operation.

So any firmware read of `0x2401 bit 7` should be analyzed as an external / pulled-up port input candidate, not as an interrupt flag. Tiny address-bit swap, massive interpretive faceplant. Classic.

## Reset Net

`IC4 pin34 /RESET` is tied to:

- `IC7 pin8 /RESET`
- External connector `D7`

On the external board, `D7` goes to `IC001:4` (`SE134`, 12-pin single-row Denso IC context). This is likely a shared reset line for PIA and timer, not a PIA-controlled timer control line.

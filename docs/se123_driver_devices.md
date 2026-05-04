# SE123 Driver Devices

Two identical or similar DIP-24 devices marked `SE123` are present:

- `IC5 = SE123`, chip 1
- `IC800 = SE123`, chip 2

These are likely custom Denso multi-channel driver devices. The exact function is not yet proven.

## Shared pin observations

| Pin | Observation |
|---:|---|
| 1 | VCC |
| 11 | GND |
| 12 | GND or logic-side reference context |
| 13 | VCC |
| 24 | 12 V |

The presence of both logic supply and 12 V strongly suggests that this is not a normal logic IC. It is probably an interface or driver part, possibly with protected outputs.

## Working hypothesis

The device may be an 8-channel output driver, low-side driver, open-collector/open-drain driver, or custom Denso output interface.

Possible logic-side to output-side channel mirroring:

| Logic-side pin | Possible 12 V/output-side pin |
|---:|---:|
| 10 | 14 |
| 9 | 15 |
| 8 | 16 |
| 7 | 17 |
| 6 | 18 |
| 5 | 19 |
| 4 | 20 |
| 3 | 21 |
| 2 | 22 |

This is a hypothesis based on package symmetry and traced behavior. It needs electrical verification.

## IC5 connections

| IC5 pin | Connected to |
|---:|---|
| 4 | IC1 pin 60 |
| 5 | IC1 pin 80 |
| 14 | C4 |
| 15 | C11 |
| 16 | C12 |
| 17 | D16 |
| 18 | D14 |
| 19 | A10 |
| 20 | B12 |
| 21 | A1 |
| 22 | Not connected |
| 23 | Not connected |

## IC800 connections

| IC800 pin | Connected to |
|---:|---|
| 4 | IC1 pin 25 |
| 5 | IC1 pin 27 |
| 6 | IC1 pin 28 |
| 8 | IC1 pin 57 |
| 9 | IC1 pin 58 |
| 10 | IC1 pin 59 |
| 14 | C4 |
| 15 | D12 |
| 16 | D19 |
| 17 | D18 |
| 18 | A9 |
| 19 | C20 |
| 20 | D13 |
| 21 | D15 |
| 22 | C19 |
| 23 | Not connected |

## Important shared node: C4

- `IC5 pin 14` connects to `C4`.
- `IC800 pin 14` also connects to `C4`.
- `C4` goes to `SE074 pin 15`.

This shared output or signal path needs special attention. It may be a common diagnostic, shared output, supply-related node or part of a driver grouping.

## External output clues

### A1 path

- `IC5 pin 21` goes to connector `A1`.
- The `A1` path has a transistor marked `23` with an underline in a SOT23-like package.

### C11 path

- `IC5 pin 15` goes to `C11`.
- There is an unpopulated resistor to a transistor.
- The path goes to the external ECU connector.

### C12 path

- `IC5 pin 16` goes to `C12`.
- There is a `4.3k` resistor and a transistor.
- The path goes external.

These paths support the theory that the 12 V side of the `SE123` devices is output-related rather than input-only.

## Test plan

Recommended non-destructive tests:

1. Identify supply pins with resistance and diode-mode checks.
2. Check each suspected output pin for pull-up/pull-down behavior unpowered and powered.
3. Inject safe current-limited logic levels into suspected input pins only after confirming protection paths.
4. Observe output pins with a current-limited load to 12 V or ground, depending on suspected topology.
5. Compare behavior between `IC5` and `IC800` channels.
6. Correlate MCU pins with firmware writes or peripheral control accesses.

## Warnings

Do not assume the `SE123` can source or sink arbitrary current until tested. It may include diagnostics, current limiting or clamping structures. Old automotive ICs love turning simple assumptions into smoke, because apparently that is their love language.

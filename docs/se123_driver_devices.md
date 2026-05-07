# SE123 Driver Devices

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## SE123 Notes
The SE123 devices are DIP-24 custom Denso-style parts. Current hypothesis: multi-channel output driver / low-side or open-collector style driver.

### Common Pin Context

| Pin | Observed Function |
|---:|---|
| 1 | VCC |
| 11 / 12 | GND |
| 13 | VCC |
| 24 | 12 V |

### Hypothesized Channel Mirroring

| Logic Side | 12 V / Output Side |
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

### IC5 SE123 Output Side Mapping

| IC5 Pin | ECU / Net Context |
|---:|---|
| 14 | C4 |
| 15 | C11 |
| 16 | C12 |
| 17 | D16 |
| 18 | D14 |
| 19 | A10 |
| 20 | B12 |
| 21 | A1 |
| 22 | n.c. |
| 23 | n.c. |

### IC800 SE123 Output Side Mapping

| IC800 Pin | ECU / Net Context |
|---:|---|
| 14 | C4 |
| 15 | D12 |
| 16 | D19 |
| 17 | D18 |
| 18 | A9 |
| 19 | C20 |
| 20 | D13 |
| 21 | D15 |
| 22 | C19 |
| 23 | n.c. |

---


## Current Hypothesis

IC5 and IC800 are likely Denso custom multi-channel output drivers, probably low-side or open-collector style devices with a logic side and a 12 V/output side. The project currently treats the 12 V-side pins as probable outputs, not inputs.

## Still Open

- Exact internal channel topology.
- Whether every mirrored pair is a true output channel.
- Relationship between SE123 outputs and connector-side driver stages.

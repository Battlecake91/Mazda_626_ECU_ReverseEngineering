# Secondary / Interface Board Notes

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Secondary Board Denso IC Supply Pins
| IC | +5 V | GND | +12 V | Notes |
|---|---|---|---|---|
| IC001 SE134 | Pin 7 | Pin 6 | Pins 10, 11 | 12-pin single-row Denso IC. |
| IC250 MP611 | Pin 12 | Pins 3, 15 | - | Denso custom IC. |
| IC700 SE074 | Pin 16 | Pins 3, 8, 14 | Pin 9 | Likely 3-channel injector pre-driver/driver-logic IC. |
| IC701 SE074 | Pins 3, 14, 16 | Pin 8 | Pin 9 | Likely 3-channel injector pre-driver/driver-logic IC. |

---


## Known Secondary-Board ICs

| IC | Current Role |
|---|---|
| IC001 `SE134` | Unknown 12-pin Denso custom IC; supply pins known. |
| IC250 `MP611` | Unknown Denso custom IC; tied into SE074 common line and external/interface logic. |
| IC500 `D151821-0020` | Same family as IC6; outputs are read through IC3. |
| IC700 `SE074` | Likely injector pre-driver / driver logic for one group. |
| IC701 `SE074` | Likely injector pre-driver / driver logic for timer-output group. |

## Notable Interconnects

- IC701 Pin1+2 are connected with IC700 Pin1+2, external C3 and IC250:19.
- IC701 Pins4/5/6 receive timer output paths from IC7 O1/O2/O3.
- IC700 Pins4/5/6/7 receive direct IC1 control paths.
- IC701 Pin10 drives a T801-related sense/ground-monitoring network.

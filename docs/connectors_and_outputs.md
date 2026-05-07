# ECU Connectors and Output Classes

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## ECU Connector / Pin Function Working List
This table is a working reverse-engineered mapping and should be treated as project evidence, not an official Mazda pinout.

| ECU Pin | Function | Confidence | Notes |
|---|---|---|---|
| A | 12 V BAT | High | Fuse -> diode -> overvoltage protection. |
| B | 12 V from Main Relay | High | Fuse -> capacitor. Main relay is switched by ignition switch, not by ECU. |
| F | Small low-side output | Medium | 330 ohm low-side driver. Candidate: MIL / diagnostic / small output. |
| G | Distributor / ignition-related | Medium | 2.2 ohm low-side driver. Verify exact distributor function. |
| L | A/C relay | High | 2.2 ohm low-side relay output. Pulls condenser fan low via diode logic. |
| 2G | Fused/protected input | Low-Medium | Fuse -> unknown path; exact function unresolved. |
| 2O | Purge valve | High | 3.9 ohm 2 W low-side driver. |
| 2P | Engine fan high | High | No series resistor / 0 ohm low-side driver. |
| 3A | Low-side emitter / power-GND reference | High | Not an output. No direct connection to 3C/3D according to measurement. |
| 3B | Low-side emitter / power-GND reference | High | Treat as load driver ground reference. |
| 3C | GND | High | ECU / signal ground. |
| 3D | Air-flow sensor related | Medium | Verify ADC / input filtering path. |
| 3G | Distributor / ignition-related | Medium | 3.9 ohm low-side; verify against harness / distributor. |
| 3I | VRIS 2 provision | Medium | Unpopulated on this ECU. |
| 3J | VRIS 1 | High | 3.9 ohm low-side magnetic valve class. |
| 3L | Engine fan low / condenser fan low via diode logic | Medium-High | 0 ohm low-side special relay output. |
| 3M | Fuel pressure solenoid valve | High | 3.9 ohm low-side magnetic valve class. |
| 3N | Condenser fan high | High | 0 ohm low-side special relay output. |
| 3O | EGR open solenoid | High | 3.9 ohm low-side magnetic valve class. |
| 3P | EGR close solenoid | High | 3.9 ohm low-side magnetic valve class. |
| 3Q | Idle Air Control Valve | Very High | Strong low-side / PWM-capable output. Earlier mistaken as 3A. |
| 3T | Fuel pump relay | Very High | 2.2 ohm low-side relay output. |
| 3U | Injector 1 | Very High | 82 ohm injector low-side driver group. |
| 3V | Injector 2 | Very High | 82 ohm injector low-side driver group. |
| 3W | Injector 3 | Very High | 82 ohm injector low-side driver group. |
| 3X | Injector 4 | Very High | 82 ohm injector low-side driver group. |
| 3Y | Injector 5 | Very High | 82 ohm injector low-side driver group. |
| 3Z | Injector 6 | Very High | 82 ohm injector low-side driver group. |

---


## Output Driver Classes
| Class | Pins / Signals | Notes |
|---|---|---|
| Injector low-side drivers | 3U, 3V, 3W, 3X, 3Y, 3Z | 82 ohm driver path. |
| 3.9 ohm magnetic valve outputs | 2O, 3J, 3M, 3O, 3P, possibly 3G | VRIS, purge, FPRC, EGR solenoids. |
| 2.2 ohm relay / stronger outputs | G, L, 3T | Distributor/ignition or relay outputs. |
| 0 ohm / no series resistor special outputs | 2P, 3L, 3N | Fan relay / special outputs. |
| Strong PWM output | 3Q | Idle-air control valve. |
| Small low-side output | F | 330 ohm low-side driver. Candidate MIL/diagnostic. |
| Unpopulated provision | 3I | VRIS 2 provision, unpopulated on this ECU. |
| Low-side emitter / driver ground reference | 3A, 3B | Not outputs; driver-emitter / load-ground reference area. |

Total counted low-side/output driver population from current project state: about 19 active low-side drivers, including 6 injectors and 13 non-injector outputs. 3I is a provision but unpopulated on this ECU.

---

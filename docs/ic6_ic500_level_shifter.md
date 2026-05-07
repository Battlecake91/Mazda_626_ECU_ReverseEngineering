# IC6 / IC500 D151821-0020 Comparator and Level Shifter

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## D151821-0020 / IC6 / IC500 Notes
Likely Denso comparator / 12 V to 5 V level-shifter device, DIP-24.

| Pin | Function / Behavior |
|---:|---|
| 1 | Vin +12 V |
| 12 | GND |
| 24 | Vin +5 V |
| 2-11 | Outputs 1-10, 5 V logic side |
| 23 | Input0 / comparator reference / input |
| 22 | Input1 / comparator input |
| 21 | Input2 |
| 20 | Input3 |
| 19 | Input4 |
| 18 | Input5 |
| 17 | Input6 |
| 16 | Input7 |
| 15 | Input8 |
| 14 | Input9 |
| 13 | Input10 |

Behavior noted from external source / project validation:

- Output1 Pin 2 is comparator output for Pin22 against Pin23.
- Pin2 pulls low when Pin22 voltage is lower than Pin23.
- Pin2 is open / high impedance when Pin22 is higher than Pin23.
- Outputs2-8 are +5 V with floating input and 0 V when corresponding input is grounded.
- Outputs9-10 are 0 V with floating input and +5 V when +12 V is applied to corresponding input.

---


## Usage in This ECU

- IC6 is present on the main board.
- IC500 is on the secondary/interface board and appears to be the same family/device.
- IC500 outputs are routed through the ribbon cable into IC3, where firmware can read them via the memory-mapped input-port window.
- IC6 also participates in `Schaltung 1`, where a comparator/status path is inverted through IC20 Gate 2 and fed to IC1 Pin9.

## Caution

This device must not be documented as a simple 10-channel 12 V to 5 V level shifter. Its channels appear to have mixed behavior: comparator output, ground-detect style outputs and +12 V-detect style outputs.

# IC1 MCU Pin Mapping

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Known IC1 Pin Connections

| IC1 Pin | Signal / Connection | Notes |
|---:|---|---|
| 3 | IC700:6 via external D6, R10 pull-up | SE074 / injector-related control path. |
| 4 | IC700:5 via external C6, R9 pull-up | SE074 / injector-related control path. |
| 5 | IC700:4 via external C5, R8 pull-up | SE074 / injector-related control path. |
| 6 | IC701:7 via external C17, R7 pull-up | Possible enable/bank/mode for SE074 path. |
| 9 | From IC20:6 | Inverted IC6-related status / comparator signal. |
| 11 | From IC20:8 via R606 | IC20 Gate 3 / C10 node, unresolved. |
| 14 | IC17 `/E1`, IC20:12/13 | External decoder enable / ROM select relationship. |
| 15 | A14 | ROM high address / decode. |
| 16 | A13 | ROM high address / decode. |
| 17 | A12 | Shared address bus. |
| 18 | A11 | Shared address bus. |
| 19 | A10 | Shared address bus and IC17 input. |
| 20 | A9 | Shared address bus. |
| 21 | A8 | Shared address bus. |
| 22 | VCC | Supply. |
| 23 | GND | Ground. |
| 25 | IC800:4 | SE123-related path. |
| 26 | IC700:7 via external C8, R18 pull-up | SE074 / injector-related control path. |
| 27 | IC800:5 | SE123-related path. |
| 28 | D0 | Data bus. |
| 29 | D1 | Data bus. |
| 30 | D2 | Data bus. |
| 31 | D3 | Data bus. |
| 32 | D4 | Data bus. |
| 33 | D5 | Data bus. |
| 34 | D6 | Data bus. |
| 35 | D7 | Data bus. |
| 40 | GND | Ground. |
| 41 | VCC | Supply. |
| 44 / 45 | XTAL | 8 MHz crystal. |
| 46 | GND | Ground. |
| 47 | R/W via F3 | Bus read/write direction. |
| 48 | E / bus-enable via F4 | Bus enable, also clocks IC9 divider. |
| 49-56 | IO0-IO7 / D0-D7 context | Shared ROM/RAM data bus according to earlier mapping. |
| 57 | IC800:8 | SE123-related path. |
| 58 | IC800:9 | SE123-related path. |
| 59 | IC800:10 | SE123-related path. |
| 60 | IC5:4 | SE123-related path. |
| 66 | VCC | Supply. |
| 71 | A7 | Address bus. |
| 72 | A6 | Address bus. |
| 73 | A5 | Address bus. |
| 74 | A4 | Address bus. |
| 75 | A3 | Address bus. |
| 76 | A2 | Address bus. |
| 77 | A1 | Address bus. |
| 78 | A0 | Address bus. |
| 80 | IC5:5 | SE123-related path. |
| 83 | VCC | Supply. |

## Address Pins

| Address | IC1 Pin |
|---|---:|
| A0 | 78 |
| A1 | 77 |
| A2 | 76 |
| A3 | 75 |
| A4 | 74 |
| A5 | 73 |
| A6 | 72 |
| A7 | 71 |
| A8 | 21 |
| A9 | 20 |
| A10 | 19 |
| A11 | 18 |
| A12 | 17 |
| A13 | 16 |
| A14 | 15 |

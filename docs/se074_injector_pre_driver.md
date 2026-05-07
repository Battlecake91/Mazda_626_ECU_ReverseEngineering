# SE074 Injector Pre-Driver Hypothesis

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## SE074 / Injector Secondary Board Working Hypothesis
Current hypothesis: IC700 and IC701 SE074 are very likely 3-channel injector pre-driver / driver-logic ICs. The internal Denso transistor numbering is treated as more reliable than uncertain US wiring-plan injector numbering.

### IC701 SE074

| IC701 Pin | Connection / Function |
|---:|---|
| 1 + 2 | Connected with IC700:1+2, external C3, and IC250:19; likely common enable/diagnose/sense/mode |
| 4 | From external C14 <- IC7:27/O1 with pull-up |
| 5 | From external C15 <- IC7:3/O2 with pull-up |
| 6 | From external C16 <- IC7:6/O3 with pull-up |
| 7 | External C17 -> IC1:6 with R7 pull-up; possible enable/bank/mode |
| 10 | R825 -> T801:1; T801:2 to external GND 3C/3D; T801:3 has pull-up / divider / tantalum network |
| 11 | R763 -> T761 -> external 3Z; likely injector output group |
| 12 | R743 -> T741 -> external 3X; likely injector output group |
| 13 | R723 -> T721 -> external 3V; likely injector output group |

### IC700 SE074

| IC700 Pin | Connection / Function |
|---:|---|
| 1 + 2 | Connected with IC701:1+2, external C3, and IC250:19 |
| 4 | External C5 -> IC1:5 with R8 pull-up |
| 5 | External C6 -> IC1:4 with R9 pull-up |
| 6 | External D6 -> IC1:3 with R10 pull-up |
| 7 | External C8 -> IC1:26 with R18 pull-up |
| 11 | R753 -> T751 -> external 3Y; likely injector output group |
| 12 | R733 -> T731 -> external 3W; likely injector output group |
| 13 | R713 -> T711 -> external 3U; likely injector output group |

### Injector Group Hypothesis

| Internal Driver | External Pin | Working Function |
|---|---|---|
| T711 | 3U | Injector 1 candidate |
| T721 | 3V | Injector 2 candidate |
| T731 | 3W | Injector 3 candidate |
| T741 | 3X | Injector 4 candidate |
| T751 | 3Y | Injector 5 candidate |
| T761 | 3Z | Injector 6 candidate |

Open point: IC700 receives comparable control lines directly from IC1 rather than IC7 O1/O2/O3, so the exact bank/channel/phase logic remains to be determined.

---


## Working Model

IC700 and IC701 are treated as likely 3-channel injector pre-driver / driver-logic ICs.

IC701 receives three timer-derived signals from IC7 via C14/C15/C16. IC700 receives similar-looking control lines directly from IC1. That asymmetry is important and remains unresolved: either IC1 generates the second bank directly, or SE074 contains bank/phase/mode behavior that has not yet been decoded.

## Caution About External Wiring Diagrams

The internal Denso numbering (`T711`, `T721`, `T731`, `T741`, `T751`, `T761`) appears systematic and may be more reliable than uncertain external US wiring-plan injector numbering. External diagrams are useful, but not gospel. Humanity tried that already; it invented wrong pinouts.

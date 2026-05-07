# Open Questions and Next Checks

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## Open / To Be Confirmed
| Topic | Current State | Next Useful Action |
|---|---|---|
| ROM start address | Likely `0x8000-0xFFFF` for 27C256 | Confirm using reset vector at EPROM offset `0x7FFE/0x7FFF`. |
| IC3 input bit semantics | Electrical sources known | Determine meaning of IC500/IC6 signals by tracing secondary board / firmware accesses. |
| F pin output | 330 ohm small low-side driver | Determine whether MIL, diagnostic, or small relay/logical output. |
| 3G / G distributor functions | Both ignition/distributor-related candidates | Verify exact distributor signal names by harness / oscilloscope. |
| IC20 Gate 3 / C10 node | Timing/reset/status candidate | Trace external C10 node and IC1:11 behavior. |
| IC6:22 / Schaltung 1 | IC6 comparator/status path into IC1:9 via IC20 Gate2 | Determine physical input source and firmware meaning. |
| SE074 logic | Injector pre-driver logic strongly suspected | Determine exact channel/bank/phase behavior between IC7 outputs, IC1 direct lines, IC700/IC701 and injector outputs. |
| External connector pinout | Working list exists | Continue verifying remaining A/B/2x/3x pins against harness and PCB topology. |

---


## Additional Useful Checks

| Topic | Why It Matters | Suggested Check |
|---|---|---|
| Exact HC11 Ghidra module name | Reproducible setup | Record local Ghidra processor module repository/release name. |
| ROM reset vector | Confirms ROM base | Compare EPROM bytes at dump offset `0x7FFE/0x7FFF` with expected vector at CPU `0xFFFE/0xFFFF`. |
| IC700 vs IC701 injector phasing | Explains 3 timer outputs for 6 injectors | Scope IC7 O1/O2/O3, IC1 direct SE074 lines and injector transistor gates together. |
| IC3 bit semantics | Turns electrical map into software meaning | Cross-reference firmware reads from `0x2800` with IC500 input tracing. |
| IC20 Gate 3 / C10 node | Unknown status/timing/reset function | Trace C10 node and observe IC1:11 during reset and runtime. |
| SE123 exact behavior | Separates driver outputs from status/control pins | Bench-test channels with current limiting or infer from load-side topology. |
| Connector pin confidence | Needed for final ECU pinout | Continue measuring from connector pins to driver stages and IC pins. |

# IC3 74HC541 Memory-Mapped Input Port

> Working documentation. Not an official Mazda or Denso document. Confidence varies; measured project data has priority over generic internet pinouts.


## IC3 74HC541 Input Buffer Mapping
IC3 appears to be a memory-mapped 8-bit input buffer selected at `0x2800-0x2BFF`. It has no latch; it transparently buffers external status signals onto the data bus during selected read cycles.

### Data Bus Side

| Signal | IC3 Pin | Notes |
|---|---:|---|
| D0 | 11 | Data bus bit 0 |
| D1 | 12 | Data bus bit 1 |
| D2 | 13 | Data bus bit 2 |
| D3 | 14 | Data bus bit 3 |
| D4 | 15 | Data bus bit 4 |
| D5 | 16 | Data bus bit 5 |
| D6 | 17 | Data bus bit 6 |
| D7 | 18 | Data bus bit 7 |

### Input Side / Source Mapping

| CPU Bit | Mask | IC3 Output Pin | 74HC541 Input | IC3 Input Pin | Source | Connection |
|---:|---:|---:|---|---:|---|---|
| D0 | `0x01` | 11 | A7 | 9 | IC500 Pin 11 | Ribbon connector B9 |
| D1 | `0x02` | 12 | A6 | 8 | IC500 Pin 10 | Ribbon connector A6 |
| D2 | `0x04` | 13 | A5 | 7 | IC500 Pin 6 | Ribbon connector B7 |
| D3 | `0x08` | 14 | A4 | 6 | IC6 Pin 8 | Direct |
| D4 | `0x10` | 15 | A3 | 5 | IC500 Pin 4 | Ribbon connector B6 |
| D5 | `0x20` | 16 | A2 | 4 | IC500 Pin 8 | Ribbon connector B5 |
| D6 | `0x40` | 17 | A1 | 3 | IC500 Pin 7 | Ribbon connector A4 |
| D7 | `0x80` | 18 | A0 | 2 | IC500 Pin 3 | Ribbon connector A5 |

### Raw IC3 Input Connections

| IC3 Input | Path | Destination |
|---|---|---|
| A0 | Ribbon A5 | IC500:3 |
| A1 | Ribbon A4 | IC500:7 |
| A2 | Ribbon B5 | IC500:8 |
| A3 | Ribbon B6 | IC500:4 |
| A4 | Direct | IC6:8 |
| A5 | Ribbon B7 | IC500:6 |
| A6 | Ribbon A6 | IC500:10 |
| A7 | Ribbon B9 | IC500:11 |

### Enables

| IC3 Pin | Signal | Connection | Notes |
|---:|---|---|---|
| 1 | `/OE1` | IC17 Pin 13 `/Y2` | Address select, active low. |
| 19 | `/OE2` | IC20 Pin 3 global `/OE` | Active during read cycles. |

---


## Interpretation

IC3 is not a latch. It is a live 8-bit buffer onto the data bus. Firmware reads from the `0x2800-0x2BFF` decoded window to sample the level-shifter/comparator state coming from IC500 and one direct IC6 signal.

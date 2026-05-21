[README_puni2k.md](https://github.com/user-attachments/files/28066771/README_puni2k.md)
# Wolf CGG-2 ebusd Configuration

ebusd configuration for the **Wolf CGG-2** gas condensing boiler with **Wolf BM** control module.

Tested with:
- Wolf CGG-2-18 (Feuerungsautomat: Kromschröder, SW=0204)

---

## Bus Layout

```
QQ=03 / ZZ=08  — Feuerungsautomat (boiler controller, Kromschröder)
QQ=30 / ZZ=35  — BM control module (master/slave)
QQ=0f / ZZ=0a  — Funk-Empfänger (wireless receiver)
```

---

## Working Parameters

### Operating Data (circuit: feuerung, ZZ=08, PBSB=5022)

| Name | Register | Description |
|---|---|---|
| `temp_burner` | CC0D00 | Boiler temperature °C |
| `performance_burner` | CC6F01 | Modulation % |
| `performance_pump` | CC5727 | Pump speed % |
| `no_of_firing` | CC2602 | Burner starts (total) |
| `op_hrs_heating` | CC2A02 | Burner operating hours |
| `pressure` | CC1A27 | System pressure bar |
| `vorlauf_soll` | b80200 | Flow setpoint °C |
| `vorlauf_ist` | 280d00 | Flow actual °C |
| `warmwasser_soll` | e40300 | DHW setpoint °C |
| `warmwasser_ist` | cc0e00 | DHW actual °C |

### HG Parameters (circuit: feuerung, ZZ=08, PBSB=5022/5023)

All HG parameters support read (`r`) and write (`w`). Write uses PBSB=5023 with suffix `9d010000`.

| Parameter | Description | Range |
|---|---|---|
| HG01 | Burner differential | 5–25 K |
| HG06 | Pump mode | 0–2 |
| HG07 | Pump run-on time | 0–30 min |
| HG08 | Max flow temperature | 40–90 °C |
| HG09 | Burner lockout time | 1–30 min |
| HG10 | eBUS address | — |
| HG11 | DHW quick start | 10–60 °C |
| HG12 | Gas type | — |
| HG13 | Input E1 function | 0–11 |
| HG15 | DHW hysteresis | 1–30 K |
| HG16 | Min pump speed HC | 20–100 % |
| HG17 | Max pump speed HC | 20–100 % |
| HG21 | Min boiler temp (corrosion protection) | 20–60 °C (**>40°C strongly recommended**) |
| HG22 | Max boiler temp | °C |
| HG74 | Fan speed | U/sec |
| HG75 | DHW flow rate | l/min |
| HG90 | Burner operating hours | h |
| HG91 | Burner starts | — |

### Error History (HG80–HG89)

Ten error history registers (HG80–HG89). Raw 2-byte values, encoding is proprietary and not fully decoded.

---

## Known Limitations

- **`temp_return`** (return temperature): no sensor fitted in CGG-2 hardware — register always returns 0x7FFF
- **Passive broadcast decoding** (PBSB=0503, PBSB=0800): `b`-type passive matching does not work in ebusd v26 for these message types

---

## Bus Scan Result

```
address 08: slave  MF=Kromschroeder  SW=0204  (Feuerungsautomat)
address 0a: slave  MF=Kromschroeder  SW=0204  (Funk-Empfänger slave)
address 35: slave  MF=Kromschroeder  SW=0204  (BM slave)
```

---

## References

- [ism7mqtt](https://github.com/zivillian/ism7mqtt) — Wolf SmartSet parameter extraction (TelegramNr mapping)
- FHEM forum topic 95173 — early Wolf CGG-2 eBUS findings

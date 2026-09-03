# OTA Outdoor Measurements — 50m

**Date:** 2026-05-13  
**Setup:** Band n41 / 2593.35 MHz / 30 kHz SCS / 24 PRB  
**Hardware:** 2× USRP B205-mini, omnidirectional antennas  
**Interface:** oaitun_ue1 (5G NR tunnel), UE IP 12.1.1.137  

## Results

| Metric | Value |
|--------|-------|
| Distance | 50 m outdoor LOS |
| Ping avg | 37.0 ms |
| Ping min | 20.3 ms |
| Ping max | 139.0 ms |
| Ping jitter | 25.2 ms |
| Packet loss | 0% (20/20) |
| **DL throughput** | **~6.2 Mbps** (receiver) |
| **UL throughput** | **~0.4 Mbps** (TCP, marginal) |
| DL retransmits | 3 |
| UL retransmits | 0 |

## gNB Radio Stats

| Metric | Value |
|--------|-------|
| UL SNR | 6.5 dB |
| DL RSRP | -98 dBm |
| DL MCS | 12 |
| UL MCS | 0 (NPRB 5) |
| DL BLER | 2.2% |
| UL HARQ retx rate | ~54% (6809+3592+2245 / 23526) |
| pucch0_DTX | 166 |

## Observations

- **UL SNR 6.5 dB** — salt masiv față de 45m (18.5 dB). UE la limita acoperirii UL.
- **DL bun**: ~6.2 Mbps receiver, 3 retransmisii. gNB TX suficient de puternic.
- **UL sever limitat**: MCS 0, NPRB 5, TCP slow start nu converge în 20s. Avg real ~0.4 Mbps.
- **Ping variabil**: 20-139ms, jitter 25ms — scheduler UL face retransmisii frecvente.
- **0% packet loss** — legătura nu cade, dar jitter mare și UL scăzut.
- La această distanță, legătura este **asimetrică**: DL (gNB TX) funcționează bine, UL (UE TX) la limita sensibilității gNB.

## Concluzie

50m este **limita rangeului UL** cu antene omnidirecționale și B205-mini.  
Pentru >45m stabil: antene direcționale (Yagi/patch ≥6 dBi) sau reducere `pusch_TargetSNRx10` la 60 pentru a se adapta la SNR-ul real de ~6 dB.

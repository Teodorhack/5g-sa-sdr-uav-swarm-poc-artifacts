# OTA Outdoor Measurements — 20m

**Date:** 2026-05-13  
**Setup:** Band n41 / 2593.35 MHz / 30 kHz SCS / 24 PRB  
**Hardware:** 2× USRP B205-mini, omnidirectional antennas  
**Interface:** oaitun_ue1 (5G NR tunnel), UE IP 12.1.1.131  

## Results

| Metric | Value |
|--------|-------|
| Distance | 20 m outdoor LOS |
| Ping avg | 38.9 ms |
| Ping min | 25.8 ms |
| Ping max | 45.2 ms |
| Ping jitter | 5.2 ms |
| Packet loss | 0% (20/20) |
| **DL throughput** | **~9.0 Mbps** (receiver) |
| **UL throughput** | **~4.2 Mbps** (sender) |
| DL retransmits | 0 |
| UL retransmits | 0 |

## Notes
- iperf3 20s each direction, via oaitun_ue1 → 192.168.70.135 (oai-ext-dn)
- All traffic verified through 5G tunnel (not WiFi/hotspot)
- UE IP from core pool: 12.1.1.x

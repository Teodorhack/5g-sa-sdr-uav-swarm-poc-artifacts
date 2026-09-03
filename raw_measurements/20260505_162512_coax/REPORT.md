# 5G SA Private Network — RF Performance Measurement Report

> **For scientific / academic use.**  
> Generated: 2026-05-05 17:31:53  
> Raw data: `results/20260505_162512/raw/`  
> CSVs for plotting: `results/20260505_162512/*.csv`


---


## Abstract

This document presents systematic radio and application-layer performance measurements
of a 5G Standalone (SA) private network built with OpenAirInterface (OAI) software
and USRP B205-mini software-defined radios. The testbed operates on Band n78
(3748.8 MHz) with 24 resource blocks and 30 kHz subcarrier spacing.
Measurements cover TCP/UDP throughput, latency, packet loss, jitter, and
radio link quality indicators (SNR, MCS, BLER) under controlled RF conditions
(SDR-to-SDR coaxial connection, 1× splitter per channel for spectrum monitoring).


---


## 1. System Description


### 1.1 Network Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  Host: tech (192.168.0.102, x86, Ubuntu 22.04)             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  OAI 5G Core (Docker):                               │  │
│  │  NRF · UDR · UDM · AUSF · AMF · SMF · UPF · ext-dn  │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  oai-gnb (Docker) — USRP B205-mini (serial 3292BC5) │  │
│  │  TX/RX: Band n78, 3748.8 MHz, 24 PRB, SCS 30 kHz    │  │
│  └──────────┬───────────────────────────────────────────┘  │
└─────────────│───────────────────────────────────────────────┘
              │ coaxial cable
              ├──[splitter DL]──────────── spectrum analyzer
              │  (−3 dB)
              │
┌─────────────│───────────────────────────────────────────────┐
│             │ coaxial cable                                  │
│  ┌──────────┴───────────────────────────────────────────┐  │
│  │  oai-nr-ue (Docker) — USRP B205-mini (serial 329352C)│  │
│  │  TX/RX: same band, oaitun_ue1 IP 12.1.1.130/24       │  │
│  └──────────┬───────────────────────────────────────────┘  │
│             │ UL path: splitter ── spectrum analyzer        │
│  Host: net  │ (192.168.1.2, x86, Ubuntu 22.04)             │
└─────────────┘
```


### 1.2 RF Path Budget

| Path | Component | Loss (dB) | Notes |
|------|-----------|-----------|-------|
| DL (gNB TX → UE RX) | Coaxial cable | ≈ 0.5 | short SMA-SMA |
| DL | 2-way splitter | 3.0 | one port to UE, one to analyzer |
| DL | SMA connectors | ≈ 0.5 | 2× |
| **DL total** | | **≈ 4 dB** | |
| UL (UE TX → gNB RX) | Coaxial cable | ≈ 0.5 | |
| UL | 2-way splitter | 3.0 | one port to gNB, one to analyzer |
| UL | SMA connectors | ≈ 0.5 | 2× |
| **UL total** | | **≈ 4 dB** | |


### 1.3 PHY / MAC Configuration

| Parameter | Value | Notes |
|-----------|-------|-------|
| NR Band | n78 | FR1, 3300–4200 MHz |
| Center frequency | 3748.8 MHz | ARFCN 649920 |
| SSB frequency | 3604.8 MHz | SSB ARFCN 640320 |
| Subcarrier spacing | 30 kHz (μ=1) |  |
| Slot duration | 0.5 ms | 2 slots/subframe |
| Frame structure | TDD | OAI default pattern |
| Resource blocks (PRB) | 24 | ≈ 8.64 MHz occupied BW |
| MIMO | SISO (1T1R) | B205-mini single antenna |
| Max DL MCS | 27 (64-QAM 3/4) | OAI table 5.1.3.1-2 |
| Max UL MCS | 27 (64-QAM 3/4) |  |
| gNB max_rxgain | 114 | software AGC limit |
| UE max_rxgain | 114 |  |
| AMF IP | 192.168.70.132 | Docker network |
| UPF IP | 192.168.70.134 | GTP endpoint |
| UE PDU session IP | 12.1.1.130/24 | oaitun_ue1 |
| DNN | oai |  |
| PLMN | MCC=001 / MNC=01 | private |
| IMSI | 001010000000040 |  |


---


## 2. Radio Link Quality


### 2.1 Key Radio Indicators per Test Phase

| Phase | UL SNR (dB) | RSRP (dBm) | UL MCS | DL MCS | UL BLER | DL BLER |
|-------|------------|------------|--------|--------|---------|---------|
| Preflight (idle) | 22.0 dB | -82 dBm | QPSK (MCS 3) | QPSK (MCS 0) | 0.0000 | 0.7181 |
| T1 TCP UL solo | 11.5 dB | -82 dBm | QPSK (MCS 8) | QPSK (MCS 4) | 0.0692 | 0.0958 |
| T2 TCP DL solo | 23.5 dB | -82 dBm | 16-QAM (MCS 10) | QPSK (MCS 8) | 0.0334 | 0.0999 |
| T3 TCP bidir | 12.0 dB | -82 dBm | QPSK (MCS 6) | QPSK (MCS 6) | 0.1009 | 0.1141 |
| T4 UDP UL | 13.0 dB | -82 dBm | QPSK (MCS 6) | QPSK (MCS 2) | 0.0627 | 0.0000 |
| T5 UDP DL | 25.0 dB | -83 dBm | QPSK (MCS 6) | QPSK (MCS 5) | 0.1607 | 0.0610 |
| T8 Latency under load | 14.0 dB | N/A | QPSK (MCS 9) | 16-QAM (MCS 10) | 0.2242 | 0.0358 |
| Final (post-test) | 14.0 dB | N/A | QPSK (MCS 9) | 16-QAM (MCS 10) | 0.2242 | 0.0358 |


### 2.2 Notes on Radio Conditions

- **UL SNR during solo TCP UL:** 11.5 dB — marginal link quality (<20 dB)
- **RSRP** is consistent across phases, confirming stable physical connection.
- **BLER target:** <0.10 (10%) for HARQ efficiency. Values above indicate retransmission overhead.


---


## 3. TCP Throughput Analysis


### 3.1 Summary Statistics (600-second sustained flows)

| Test | Direction | Mean (Mbps) | Std (Mbps) | Min (Mbps) | Median (Mbps) | P95 (Mbps) | Max (Mbps) | Retx | Duration |
|------|-----------|------------|-----------|-----------|------------|-----------|-----------|------|----------|
| T1 TCP UL (solo) | UL | 0.97 | 1.44 | 0.00 | 0.00 | 3.15 | 5.45 | 3174 | 600s |
| T2 TCP DL (solo) | DL | 2.38 | 0.41 | 1.26 | 2.52 | 2.94 | 3.36 | 367 | 600s |
| T3 TCP UL (bidir) | — | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |


### 3.2 Throughput Efficiency vs. Theoretical Peak

Estimated theoretical peak for 24 PRB, μ=1, SISO, MCS 27 (64-QAM rate 3/4):

| Parameter | Value |
|-----------|-------|
| PRBs | 24 |
| Subcarriers per PRB | 12 |
| OFDM symbols per slot | 14 |
| Slots per second | 2000 (μ=1) |
| TDD DL ratio (est.) | ~70% |
| Modulation order (MCS 27) | 6 bits/symbol (64-QAM) |
| Code rate (MCS 27) | ~0.75 |
| DMRS overhead | ~12.5% |
| Est. peak DL PHY rate | ≈ 24×12×14×2000×0.7×6×0.75×0.875 / 1e6 ≈ **12.6 Mbps** |
| Est. peak UL PHY rate | ≈ 30% TDD UL share → ≈ **5.4 Mbps** |

| Metric | Measured | Theoretical Peak | Efficiency |
|--------|----------|-----------------|------------|
| TCP UL throughput | 0.97 Mbps | ≈ 5.4 Mbps | 18.0% |
| TCP DL throughput | 2.38 Mbps | ≈ 12.6 Mbps | 18.9% |


### 3.3 TCP UL — Per-5s Interval Throughput (T1, 600s)

| t (s) | Throughput (Mbps) | Retx | CWND (KB) |
|-------|------------------|------|-----------|
| 0–5 | 0.838 | 0 | 76.4 |
| 5–10 | 2.097 | 0 | 267.3 |
| 10–15 | 2.516 | 0 | 476.5 |
| 15–20 | 1.887 | 0 | 685.8 |
| 20–25 | 0.000 | 0 | 896.5 |
| 25–30 | 2.307 | 0 | 1104.4 |
| 30–35 | 0.000 | 1 | 1274.1 |
| 35–40 | 0.000 | 0 | 1145.4 |
| 40–45 | 0.000 | 0 | 1013.9 |
| 45–50 | 2.726 | 0 | 905.0 |
| 50–55 | 0.000 | 0 | 1022.4 |
| 55–60 | 0.000 | 0 | 1122.8 |
| 60–65 | 2.305 | 0 | 1207.6 |
| 65–70 | 0.000 | 0 | 1407.0 |
| 70–75 | 0.000 | 0 | 1646.0 |
| 75–80 | 2.097 | 0 | 1883.5 |
| 80–85 | 0.000 | 0 | 2108.4 |
| 85–90 | 0.000 | 0 | 2333.2 |
| 90–95 | 0.000 | 0 | 2570.8 |
| 95–100 | 2.097 | 1 | 2750.4 |
| 100–105 | 0.000 | 0 | 2601.9 |
| 105–110 | 0.000 | 0 | 1961.3 |
| 110–115 | 0.000 | 0 | 1961.3 |
| 115–120 | 0.000 | 0 | 1961.3 |
| 120–125 | 0.000 | 0 | 1961.3 |
| 125–130 | 5.453 | 1 | 1986.8 |
| 130–135 | 0.000 | 0 | 2143.7 |
| 135–140 | 0.000 | 0 | 2296.4 |
| 140–145 | 2.307 | 0 | 2409.6 |
| 145–150 | 0.000 | 21 | 2297.9 |
| 150–155 | 0.000 | 0 | 2139.5 |
| 155–160 | 0.000 | 0 | 1709.6 |
| 160–165 | 0.000 | 0 | 1709.6 |
| 165–170 | 5.033 | 0 | 1711.0 |
| 170–175 | 0.000 | 0 | 1786.0 |
| 175–180 | 0.000 | 0 | 1868.0 |
| 180–185 | 2.307 | 0 | 1944.3 |
| 185–190 | 0.000 | 0 | 2136.6 |
| 190–195 | 2.097 | 0 | 2432.2 |
| 195–200 | 0.000 | 0 | 2732.0 |
| 200–205 | 0.000 | 0 | 3037.4 |
| 205–210 | 1.887 | 0 | 3342.8 |
| 210–215 | 2.727 | 0 | 3646.9 |
| 215–220 | 0.000 | 0 | 3924.0 |
| 220–225 | 0.000 | 1 | 2751.8 |
| 225–230 | 0.000 | 0 | 2751.8 |
| 230–235 | 0.000 | 0 | 2751.8 |
| 235–240 | 0.000 | 0 | 2751.8 |
| 240–245 | 5.243 | 132 | 2751.8 |
| 245–250 | 0.000 | 0 | 2825.3 |
| 250–255 | 0.000 | 0 | 3009.1 |
| 255–260 | 2.097 | 0 | 3164.7 |
| 260–265 | 0.000 | 296 | 2220.1 |
| 265–270 | 0.000 | 190 | 2220.1 |
| 270–275 | 0.000 | 0 | 2220.1 |
| 275–280 | 0.000 | 0 | 2220.1 |
| 280–285 | 0.000 | 0 | 2220.1 |
| 285–290 | 5.453 | 0 | 2232.8 |
| 290–295 | 0.000 | 0 | 2316.2 |
| 295–300 | 2.097 | 0 | 2406.7 |
| 300–305 | 0.000 | 0 | 2490.2 |
| 305–310 | 0.000 | 0 | 2683.9 |
| 310–315 | 1.887 | 0 | 2992.2 |
| 315–320 | 0.000 | 0 | 3262.2 |
| 320–325 | 2.307 | 0 | 3559.2 |
| 325–330 | 0.000 | 0 | 3860.4 |
| 330–335 | 2.936 | 0 | 4141.8 |
| 335–340 | 0.000 | 0 | 4458.5 |
| 340–345 | 0.000 | 0 | 4783.8 |
| 345–350 | 3.146 | 0 | 5109.0 |
| 350–355 | 0.000 | 0 | 5246.2 |
| 355–360 | 0.000 | 0 | 5246.2 |
| 360–365 | 0.000 | 0 | 5246.2 |
| 365–370 | 0.000 | 0 | 5246.2 |
| 370–375 | 0.000 | 0 | 5246.2 |
| 375–380 | 0.000 | 0 | 5246.2 |
| 380–385 | 0.000 | 1 | 5249.0 |
| 385–390 | 0.000 | 0 | 5249.0 |
| 390–395 | 0.000 | 0 | 5249.0 |
| 395–400 | 0.000 | 665 | 470.9 |
| 400–405 | 0.000 | 1066 | 1224.6 |
| 405–410 | 2.307 | 612 | 1979.7 |
| 410–415 | 2.307 | 0 | 2015.0 |
| 415–420 | 0.000 | 0 | 3683.6 |
| 420–425 | 2.937 | 0 | 3866.0 |
| 425–430 | 0.000 | 0 | 4117.8 |
| 430–435 | 0.000 | 0 | 4328.4 |
| 435–440 | 3.146 | 0 | 4510.9 |
| 440–445 | 0.000 | 0 | 4728.6 |
| 445–450 | 0.000 | 157 | 2164.9 |
| 450–455 | 0.000 | 0 | 2164.9 |
| 455–460 | 2.097 | 0 | 2164.9 |
| 460–465 | 3.565 | 0 | 3413.5 |
| 465–470 | 0.000 | 0 | 3536.6 |
| 470–475 | 2.517 | 0 | 3652.5 |
| 475–480 | 0.000 | 0 | 3757.2 |
| 480–485 | 2.936 | 0 | 3943.8 |
| 485–490 | 0.000 | 0 | 4318.5 |
| 490–495 | 3.146 | 0 | 4720.1 |
| 495–500 | 0.000 | 0 | 5123.1 |
| 500–505 | 2.936 | 0 | 5538.9 |
| 505–510 | 0.000 | 0 | 5946.1 |
| 510–515 | 0.000 | 0 | 6357.6 |
| 515–520 | 3.146 | 0 | 6422.7 |
| 520–525 | 0.000 | 0 | 6422.7 |
| 525–530 | 2.936 | 0 | 6422.7 |
| 530–535 | 0.000 | 0 | 6422.7 |
| 535–540 | 3.145 | 0 | 6422.7 |
| 540–545 | 0.000 | 0 | 6422.7 |
| 545–550 | 0.000 | 0 | 6422.7 |
| 550–555 | 2.935 | 0 | 6422.7 |
| 555–560 | 0.000 | 0 | 6422.7 |
| 560–565 | 2.307 | 27 | 2015.0 |
| 565–570 | 0.000 | 0 | 2015.0 |
| 570–575 | 0.000 | 2 | 2012.2 |
| 575–580 | 2.726 | 0 | 2012.2 |
| 580–585 | 2.306 | 0 | 4496.7 |
| 585–590 | 0.000 | 0 | 4656.5 |
| 590–595 | 3.146 | 1 | 3413.5 |
| 595–600 | 0.000 | 0 | 3413.5 |


### 3.4 TCP DL — Per-5s Interval Throughput (T2, 600s)

| t (s) | Throughput (Mbps) | Retx | CWND (KB) |
|-------|------------------|------|-----------|
| 0–5 | 1.467 | 0 | 0.0 |
| 5–10 | 1.678 | 0 | 0.0 |
| 10–15 | 1.468 | 0 | 0.0 |
| 15–20 | 1.678 | 0 | 0.0 |
| 20–25 | 1.468 | 0 | 0.0 |
| 25–30 | 1.468 | 0 | 0.0 |
| 30–35 | 1.258 | 0 | 0.0 |
| 35–40 | 1.678 | 0 | 0.0 |
| 40–45 | 1.678 | 0 | 0.0 |
| 45–50 | 1.678 | 0 | 0.0 |
| 50–55 | 1.678 | 0 | 0.0 |
| 55–60 | 1.468 | 0 | 0.0 |
| 60–65 | 1.678 | 0 | 0.0 |
| 65–70 | 1.887 | 0 | 0.0 |
| 70–75 | 1.678 | 0 | 0.0 |
| 75–80 | 1.887 | 0 | 0.0 |
| 80–85 | 1.887 | 0 | 0.0 |
| 85–90 | 1.887 | 0 | 0.0 |
| 90–95 | 1.887 | 0 | 0.0 |
| 95–100 | 1.887 | 0 | 0.0 |
| 100–105 | 1.887 | 0 | 0.0 |
| 105–110 | 1.887 | 0 | 0.0 |
| 110–115 | 2.097 | 0 | 0.0 |
| 115–120 | 1.887 | 0 | 0.0 |
| 120–125 | 2.097 | 0 | 0.0 |
| 125–130 | 2.097 | 0 | 0.0 |
| 130–135 | 2.097 | 0 | 0.0 |
| 135–140 | 2.097 | 0 | 0.0 |
| 140–145 | 2.097 | 0 | 0.0 |
| 145–150 | 2.307 | 0 | 0.0 |
| 150–155 | 2.307 | 0 | 0.0 |
| 155–160 | 2.307 | 0 | 0.0 |
| 160–165 | 2.307 | 0 | 0.0 |
| 165–170 | 2.097 | 0 | 0.0 |
| 170–175 | 2.517 | 0 | 0.0 |
| 175–180 | 2.307 | 0 | 0.0 |
| 180–185 | 2.517 | 0 | 0.0 |
| 185–190 | 2.307 | 0 | 0.0 |
| 190–195 | 2.517 | 0 | 0.0 |
| 195–200 | 2.517 | 0 | 0.0 |
| 200–205 | 2.517 | 0 | 0.0 |
| 205–210 | 2.517 | 0 | 0.0 |
| 210–215 | 2.726 | 0 | 0.0 |
| 215–220 | 2.097 | 0 | 0.0 |
| 220–225 | 2.307 | 0 | 0.0 |
| 225–230 | 2.097 | 0 | 0.0 |
| 230–235 | 2.726 | 0 | 0.0 |
| 235–240 | 2.307 | 0 | 0.0 |
| 240–245 | 2.307 | 0 | 0.0 |
| 245–250 | 2.517 | 0 | 0.0 |
| 250–255 | 2.517 | 0 | 0.0 |
| 255–260 | 2.726 | 0 | 0.0 |
| 260–265 | 2.097 | 0 | 0.0 |
| 265–270 | 2.517 | 0 | 0.0 |
| 270–275 | 2.307 | 0 | 0.0 |
| 275–280 | 2.097 | 0 | 0.0 |
| 280–285 | 2.307 | 0 | 0.0 |
| 285–290 | 2.517 | 0 | 0.0 |
| 290–295 | 2.307 | 0 | 0.0 |
| 295–300 | 2.517 | 0 | 0.0 |
| 300–305 | 2.517 | 0 | 0.0 |
| 305–310 | 2.097 | 0 | 0.0 |
| 310–315 | 2.517 | 0 | 0.0 |
| 315–320 | 2.517 | 0 | 0.0 |
| 320–325 | 2.517 | 0 | 0.0 |
| 325–330 | 2.517 | 0 | 0.0 |
| 330–335 | 2.517 | 0 | 0.0 |
| 335–340 | 2.517 | 0 | 0.0 |
| 340–345 | 2.517 | 0 | 0.0 |
| 345–350 | 2.726 | 0 | 0.0 |
| 350–355 | 2.517 | 0 | 0.0 |
| 355–360 | 2.517 | 0 | 0.0 |
| 360–365 | 2.726 | 0 | 0.0 |
| 365–370 | 2.307 | 0 | 0.0 |
| 370–375 | 2.726 | 0 | 0.0 |
| 375–380 | 2.517 | 0 | 0.0 |
| 380–385 | 2.517 | 0 | 0.0 |
| 385–390 | 2.726 | 0 | 0.0 |
| 390–395 | 2.517 | 0 | 0.0 |
| 395–400 | 2.726 | 0 | 0.0 |
| 400–405 | 2.726 | 0 | 0.0 |
| 405–410 | 2.726 | 0 | 0.0 |
| 410–415 | 2.727 | 0 | 0.0 |
| 415–420 | 2.516 | 0 | 0.0 |
| 420–425 | 2.517 | 0 | 0.0 |
| 425–430 | 2.936 | 0 | 0.0 |
| 430–435 | 2.727 | 0 | 0.0 |
| 435–440 | 2.516 | 0 | 0.0 |
| 440–445 | 2.726 | 0 | 0.0 |
| 445–450 | 2.726 | 0 | 0.0 |
| 450–455 | 2.727 | 0 | 0.0 |
| 455–460 | 2.726 | 0 | 0.0 |
| 460–465 | 2.726 | 0 | 0.0 |
| 465–470 | 2.517 | 0 | 0.0 |
| 470–475 | 2.517 | 0 | 0.0 |
| 475–480 | 2.516 | 0 | 0.0 |
| 480–485 | 2.726 | 0 | 0.0 |
| 485–490 | 2.517 | 0 | 0.0 |
| 490–495 | 2.937 | 0 | 0.0 |
| 495–500 | 2.726 | 0 | 0.0 |
| 500–505 | 2.726 | 0 | 0.0 |
| 505–510 | 2.726 | 0 | 0.0 |
| 510–515 | 2.727 | 0 | 0.0 |
| 515–520 | 2.726 | 0 | 0.0 |
| 520–525 | 2.726 | 0 | 0.0 |
| 525–530 | 2.936 | 0 | 0.0 |
| 530–535 | 2.727 | 0 | 0.0 |
| 535–540 | 2.936 | 0 | 0.0 |
| 540–545 | 3.146 | 0 | 0.0 |
| 545–550 | 2.726 | 0 | 0.0 |
| 550–555 | 3.356 | 0 | 0.0 |
| 555–560 | 2.936 | 0 | 0.0 |
| 560–565 | 2.726 | 0 | 0.0 |
| 565–570 | 2.726 | 0 | 0.0 |
| 570–575 | 2.517 | 0 | 0.0 |
| 575–580 | 2.726 | 0 | 0.0 |
| 580–585 | 2.517 | 0 | 0.0 |
| 585–590 | 2.517 | 0 | 0.0 |
| 590–595 | 2.936 | 0 | 0.0 |
| 595–600 | 2.726 | 0 | 0.0 |


### 3.5 10-Minute Segment Analysis

Statistics computed per 10-minute window from 5-second interval data.

**T1 TCP UL**

| Segment | Mean (Mbps) | Std (Mbps) | Min (Mbps) | Median (Mbps) | Max (Mbps) | Samples |
|---------|------------|-----------|-----------|--------------|-----------|---------|
| 0–10 min | 0.97 | 1.44 | 0.00 | 0.00 | 5.45 | 120 |

**T2 TCP DL**

| Segment | Mean (Mbps) | Std (Mbps) | Min (Mbps) | Median (Mbps) | Max (Mbps) | Samples |
|---------|------------|-----------|-----------|--------------|-----------|---------|
| 0–10 min | 2.38 | 0.41 | 1.26 | 2.52 | 3.36 | 120 |


### 3.6 Parallel Streams

| Config | Dir | Streams | Total Mean (Mbps) | Retx | Duration |
|--------|-----|---------|------------------|------|----------|
| P2 | UL | 2 | 1.49 | 0 | 120s |
| P2 | DL | 2 | N/A | N/A | N/A |
| P4 | UL | 4 | N/A | N/A | N/A |
| P4 | DL | 4 | N/A | N/A | N/A |


---


## 4. UDP Performance Analysis


### 4.1 UDP Uplink — Bitrate Sweep (UE → ext-dn, 60s per rate)

| Target (Mbps) | Achieved (Mbps) | Utilization (%) | Jitter (ms) | Loss (%) | Lost pkts | Total pkts |
|--------------|-----------------|----------------|------------|----------|-----------|------------|
| 1 | 1.00 | 100.0 | 4.05 | 0.00 | 0 | 5180 |
| 2 | 2.00 | 100.0 | 0.00 | 0.00 | 0 | 10359 |
| 4 | 4.00 | 100.0 | 0.00 | 0.00 | 0 | 20718 |
| 6 | 6.00 | 100.0 | 0.00 | 0.00 | 0 | 31077 |
| 8 | N/A | N/A | N/A | N/A | N/A | N/A |
| 10 | 10.00 | 100.0 | 0.00 | 0.00 | 0 | 51795 |


### 4.2 UDP Downlink — Bitrate Sweep (ext-dn → UE, 60s per rate)

| Target (Mbps) | Achieved (Mbps) | Utilization (%) | Jitter (ms) | Loss (%) | Lost pkts | Total pkts |
|--------------|-----------------|----------------|------------|----------|-----------|------------|
| 1 | N/A | N/A | N/A | N/A | N/A | N/A |
| 2 | N/A | N/A | N/A | N/A | N/A | N/A |
| 4 | 4.00 | 100.0 | 1.15 | 0.23 | 47 | 20817 |
| 6 | 6.00 | 100.0 | 2.06 | 0.09 | 29 | 31158 |
| 8 | 8.00 | 100.0 | 2.55 | 0.08 | 33 | 41629 |
| 10 | 10.00 | 100.0 | 1.90 | 0.06 | 31 | 52025 |


### 4.3 Observations

- **Low target rates (1–2 Mbps):** should achieve near-zero loss if below UL/DL peak capacity.
- **High target rates (8–10 Mbps):** expected packet loss when target exceeds link capacity.
- **Jitter:** characterizes buffer management and scheduling granularity (slot = 0.5 ms).
- **Minimum achievable jitter** bounded by 5G NR slot duration: 0.5 ms (μ=1, 30 kHz SCS).


---


## 5. Latency Analysis


### 5.1 RTT Summary

All measurements: ICMP echo (ping) via oaitun_ue1 (5G PDU session) to ext-dn (192.168.70.135).

| Scenario | Pkts | Interval | RTT min (ms) | RTT avg (ms) | RTT max (ms) | mdev (ms) | Loss (%) |
|----------|------|----------|------------|------------|------------|----------|----------|
| Preflight (idle) | 20 | 0.5s | 34.09 | 44.45 | 53.49 | 6.80 | 0.0 |
| Idle (no traffic) | 300 | 1.0s | N/A | N/A | N/A | N/A | N/A |
| Under TCP UL load | 180 | 1.0s | N/A | N/A | N/A | N/A | N/A |


### 5.2 RTT vs. Packet Size (idle, ICMP, 100 pkts × 0.2s)

Reveals serialization delay and potential fragmentation effects.

| ICMP payload (B) | IP frame (B) | Ethernet frame (B) | RTT min (ms) | RTT avg (ms) | RTT max (ms) | mdev (ms) |
|-----------------|-------------|------------------|------------|------------|------------|----------|
| 64 | 92 | 106 | N/A | N/A | N/A | N/A |
| 128 | 156 | 170 | N/A | N/A | N/A | N/A |
| 256 | 284 | 298 | N/A | N/A | N/A | N/A |
| 512 | 540 | 554 | N/A | N/A | N/A | N/A |
| 1024 | 1052 | 1066 | N/A | N/A | N/A | N/A |
| 1400 | 1428 | 1442 | N/A | N/A | N/A | N/A |


### 5.3 Bufferbloat Analysis

RTT under TCP UL load compared to idle baseline reveals buffer occupancy.

*Insufficient data for bufferbloat analysis.*


---


## 6. KPI Master Table

Consolidated key performance indicators for quick reference.

| Category | KPI | Value | Unit | Condition |
|----------|-----|-------|------|-----------|
| Radio | UL SNR (solo TCP UL) | 11.5 | dB | 30 dB → excellent |
| Radio | UL SNR (bidir) | 12.0 | dB | degraded by self-interference |
| Radio | RSRP | N/A | dBm | final snapshot |
| Radio | UL MCS at peak SNR | QPSK (MCS 8) | — |  |
| Radio | DL MCS at peak SNR | QPSK (MCS 8) | — |  |
| Radio | UL BLER (TCP UL load) | 0.0692 | — | target <0.10 |
| Radio | DL BLER (TCP DL load) | 0.0999 | — | target <0.10 |
| TCP | UL mean throughput (solo) | 0.97 | Mbps | 600s, 1 stream |
| TCP | UL peak throughput (solo) | 5.45 | Mbps | best 5s interval |
| TCP | UL std deviation | 1.44 | Mbps | variability |
| TCP | DL mean throughput (solo) | 2.38 | Mbps | 600s, 1 stream |
| TCP | DL peak throughput (solo) | 3.36 | Mbps | best 5s interval |
| TCP | UL mean (bidir, with DL active) | N/A | Mbps | simultaneous UL+DL |
| TCP | UL mean, P2 streams | 1.49 | Mbps | 120s |
| TCP | UL mean, P4 streams | N/A | Mbps | 120s |
| TCP | DL mean, P2 streams | N/A | Mbps | 120s |
| TCP | DL mean, P4 streams | N/A | Mbps | 120s |
| UDP UL | Jitter @1 Mbps | 4.05 | ms |  |
| UDP UL | Loss @1 Mbps | 0.00 | % |  |
| UDP UL | Jitter @2 Mbps | 0.00 | ms |  |
| UDP UL | Loss @2 Mbps | 0.00 | % |  |
| UDP DL | Jitter @1 Mbps | N/A | ms |  |
| UDP DL | Loss @1 Mbps | N/A | % |  |
| UDP DL | Jitter @2 Mbps | N/A | ms |  |
| UDP DL | Loss @2 Mbps | N/A | % |  |
| Latency | RTT avg (idle, 300 pkts) | N/A | ms | ICMP 64B payload |
| Latency | RTT min (idle) | N/A | ms |  |
| Latency | RTT max (idle) | N/A | ms |  |
| Latency | RTT mdev / jitter (idle) | N/A | ms |  |
| Latency | RTT avg (under TCP UL) | N/A | ms | bufferbloat indicator |


---


## 7. Measurement Methodology


### 7.1 Test Schedule

| ID | Test | Duration | Protocol | Direction | Parameters |
|----|----|----------|----------|-----------|------------|
| T1 | TCP UL sustained | 600s | TCP | UE→ext-dn | 1 stream, 5s intervals |
| T2 | TCP DL sustained | 600s | TCP | ext-dn→UE | 1 stream (-R), 5s intervals |
| T3 | TCP bidir simultaneous | 600s | TCP | both | UL+DL parallel |
| T4 | UDP UL sweep | 6×60s | UDP | UE→ext-dn | 1/2/4/6/8/10 Mbps target |
| T5 | UDP DL sweep | 6×60s | UDP | ext-dn→UE | 1/2/4/6/8/10 Mbps target |
| T6 | TCP parallel | 4×120s | TCP | UL+DL | P=2 and P=4 streams |
| T7 | Latency idle | 300+600pkts | ICMP | UE→ext-dn | 1s interval + size sweep |
| T8 | Latency under load | 180s | ICMP+TCP | mixed | ping during TCP UL |


### 7.2 Tools

| Tool | Version | Purpose |
|------|---------|---------|
| iperf3 | 3.x (OAI container) | TCP/UDP throughput, JSON output |
| ping (iputils) | — | RTT / ICMP latency |
| docker logs | — | gNB PHY/MAC statistics |
| OAI gNB | develop branch | SNR, MCS, BLER reporting |


### 7.3 Limitations

- **SISO (1T1R):** B205-mini has single RF path; no spatial multiplexing.
- **Splitter loss:** −3 dB per channel; included in path budget (Section 1.2).
- **TDD self-interference:** during simultaneous UL+DL, gNB TX leaks into UL RX via cable coupling → SNR degradation in T3. This is testbed-specific and not representative of a real deployment.
- **Loopback routing:** ext-dn resides on same host as gNB; DL traffic does not traverse the full internet path.
- **Single UE:** no multi-user scheduling effects.


---


## 8. Raw Data Reference

All raw files are in `results/20260505_162512/raw/`:

| File | Content |
|------|---------|
| `t1_tcp_ul.json` | iperf3 JSON — TCP UL 600s |
| `t2_tcp_dl.json` | iperf3 JSON — TCP DL 600s |
| `t3_tcp_ul_bidir.json` | iperf3 JSON — TCP UL during bidir |
| `t3_tcp_dl_bidir.txt` | iperf3 text — TCP DL during bidir (background) |
| `t4_udp_ul_*.json` | iperf3 JSON — UDP UL at each target rate |
| `t5_udp_dl_*.json` | iperf3 JSON — UDP DL at each target rate |
| `t6_tcp_ul_P*.json` | iperf3 JSON — TCP UL parallel streams |
| `t6_tcp_dl_P*.json` | iperf3 JSON — TCP DL parallel streams |
| `t7_ping_300.txt` | ping output — 300 pkts idle |
| `t7_ping_sz*.txt` | ping output — size sweep |
| `t8_ping_under_ul.txt` | ping output — under TCP UL load |
| `gnb_*.txt` | gNB docker logs snapshot (SNR/MCS/BLER) per phase |

CSVs for plotting (in `results/20260505_162512/`):

| File | Content |
|------|---------|
| `tcp_ul_timeseries.csv` | t0, t1, mbps, retx, cwnd_kb per 5s interval |
| `tcp_dl_timeseries.csv` | same for DL |
| `udp_ul_sweep.csv` | target_mbps, achieved, jitter, loss per rate |
| `udp_dl_sweep.csv` | same for DL |
| `latency_vs_pktsize.csv` | payload_b, frame_b, min/avg/max/mdev RTT |


---


*Report generated by `analyze_results.py` — 5G SA SDR measurement suite*
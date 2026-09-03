# Datasets — ICASC2026 5G-over-SDR Proof-of-Concept

Tidy CSVs derived from two real 5G Standalone / USRP B205-mini measurement campaigns.
All files are produced by `../notebook/kpi_pipeline.ipynb`. See `PROVENANCE.md` for the
exact source file and transformation behind each column. Nothing here is synthetic.

## Campaigns

- **RUN1 `20260505_162512`** — coaxial SDR-to-SDR link (20 dB attenuators + splitter to a
  spectrum analyzer), n78 3748.8 MHz, 24 PRB, SCS 30 kHz. Controlled RF, application-layer KPIs.
- **RUN2 `20260325_000034`** — over-the-air SDR link, n78 3604.8 MHz, 24 PRB, SCS 30 kHz.
  Source of the drone command-and-control (MAVLink-style) traffic results.

## Files

| File | Rows | Columns | What it is |
|------|------|---------|------------|
| `tcp_dl_timeseries.csv` | 120 | `direction,t_start_s,t_end_s,throughput_mbps,retransmits,cwnd_kb` | TCP downlink throughput per 5 s over a 600 s sustained flow (RUN1). |
| `tcp_ul_timeseries.csv` | 120 | same schema | TCP uplink throughput per 5 s over 600 s (RUN1). Bursty. |
| `udp_sweep.csv` | 9 | `direction,target_mbps,achieved_mbps,jitter_ms,loss_pct,lost,total` | UDP DL (4/6/8/10 Mbps) + UL (1/2/4/6/10 Mbps) rate sweep with jitter & loss (RUN1). |
| `ping_distribution_1000.csv` | 1000 | `icmp_seq,rtt_ms` | Per-packet idle ICMP RTT, 1000 pkts @ 1 pps (RUN2). |
| `rtt_vs_pktsize.csv` | 7 | `payload_b,rtt_min_ms,rtt_avg_ms,rtt_max_ms,mdev_ms,loss_pct,n` | RTT summary vs ICMP payload 20–1400 B (RUN2). |
| `drone_c2.csv` | 6 | `rate_pps,direction,jitter_ms,loss_pct,throughput_bps,packets` | **Key result.** MAVLink-style 50-byte UDP C2 traffic at 10/50/100 pps, UL & DL, 0% loss (RUN2). |
| `rf_phy_kpis.csv` | 8 | `phase,ul_snr_db,rsrp_dbm,ul_mcs,dl_mcs,ul_bler,dl_bler` | RF/PHY link indicators per test phase, transcribed from RUN1 REPORT.md Section 2.1. |

## Units

Throughput in Mbit/s (`throughput_bps` in bit/s for the drone file), latency/jitter in ms,
loss in percent, SNR in dB, RSRP in dBm, BLER as a fraction (0–1).

## Regenerate

```powershell
cd ..\notebook
python -m nbconvert --to notebook --execute --inplace kpi_pipeline.ipynb
```

This rewrites every CSV in this folder and every PDF in `../figures/` and `../paper/figures/`.

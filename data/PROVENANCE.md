# Provenance — ICASC2026 5G-over-SDR Proof-of-Concept

Every output CSV and figure below is derived **only** from the real measurement files listed.
No value was invented. Regenerate everything by running `notebook/kpi_pipeline.ipynb` top-to-bottom.

## Source campaigns

| Tag | Run | Type | Radio | Source root |
|-----|-----|------|-------|-------------|
| RUN1 | `20260505_162512` | coax SDR-to-SDR (20 dB attenuators, splitter to analyzer) | n78 3748.8 MHz, 24 PRB, SCS 30 kHz, USRP B205-mini | `raw_measurements/20260505_162512_coax/` (vendored in this repo) |
| RUN2 | `20260325_000034` | over-the-air SDR | n78 3604.8 MHz, 24 PRB, SCS 30 kHz, USRP B205-mini | `raw_measurements/20260325_000034_ota/raw/` (vendored in this repo) |

Both raw campaigns are committed inside this repository (not on an external drive), so the
notebook is fully reproducible from a fresh clone with no external path dependency.

## Output CSVs

| Output (`data\`) | Source file(s) | Transformation |
|------------------|----------------|----------------|
| `tcp_dl_timeseries.csv` | RUN1 `tcp_dl_timeseries.csv` | Renamed to tidy schema (`t0`→`t_start_s`, `t1`→`t_end_s`, `mbps`→`throughput_mbps`); added `direction=DL`. Values unchanged. |
| `tcp_ul_timeseries.csv` | RUN1 `tcp_ul_timeseries.csv` | Same tidy rename; `direction=UL`. Values unchanged. |
| `udp_sweep.csv` | RUN1 `udp_dl_sweep.csv`, `udp_ul_sweep.csv` | Concatenated DL+UL; renamed `mbps`→`achieved_mbps`, `lost_pct`→`loss_pct`; added `direction`. Values unchanged. |
| `ping_distribution_1000.csv` | RUN2 `raw\ping_distribution_1000.txt` | Regex-extracted the 1000 per-packet `time=<x> ms` values into `icmp_seq,rtt_ms`. |
| `rtt_vs_pktsize.csv` | RUN2 `raw\ping_size_{20,64,128,256,512,1024,1400}B.txt` | Parsed each file's `rtt min/avg/max/mdev` summary line + loss. One row per payload size. |
| `drone_c2.csv` | RUN2 `raw\iperf3_udp_drone_{ul,dl}_{10,50,100}pps.json` | Extracted `end.sum` `jitter_ms` / `lost_percent` / `bits_per_second` / `packets` per rate & direction. |
| `rf_phy_kpis.csv` | RUN1 `REPORT.md` (Section 2.1) | Verbatim transcription of the per-phase RF/PHY table (UL SNR, RSRP, UL/DL MCS, UL/DL BLER). Not a CSV in the source, so transcribed with the report cited as source. |

## Output figures (vector PDF, also copied to `paper\figures\`)

| Figure | Source file(s) | Transformation |
|--------|----------------|----------------|
| `fig_tcp_timeseries.pdf` | RUN1 `tcp_dl_timeseries.csv`, `tcp_ul_timeseries.csv` | Per-5 s throughput vs time (0–600 s), DL & UL; horizontal lines at each series mean. |
| `fig_udp_sweep.pdf` | RUN1 `udp_dl_sweep.csv`, `udp_ul_sweep.csv` | Achieved vs target rate, DL & UL; each point labelled with measured loss% + jitter; `y=x` ideal reference. |
| `fig_ping_rtt.pdf` | RUN2 `raw\ping_distribution_1000.txt` | Empirical CDF of 1000 per-packet RTTs; vertical markers at summary min/avg/max. |
| `fig_rtt_vs_pktsize.pdf` | RUN2 `raw\ping_size_*B.txt` (7 files) | avg RTT vs payload (log x); whiskers span measured min..max. |
| `fig_drone_c2.pdf` | RUN2 `raw\iperf3_udp_drone_{ul,dl}_{10,50,100}pps.json` | **Headline.** Grouped bars: UL & DL jitter per MAVLink packet rate; 0% loss annotated. |
| `fig_rf_phy.pdf` | RUN1 `REPORT.md` (Section 2.1) | Two shared-x panels: UL SNR per phase, and UL/DL BLER per phase with 0.10 HARQ target line. |

## Integrity notes

- No figure mixes measured and estimated data. All six figures are 100% measured.
- No ESTIMATE-labelled figure was needed.
- Drone jitter/loss parsed from JSON match RUN2 `REPORT.md` Test 10 exactly
  (UL 5.29 / 17.71 / 12.17 ms; DL 0.61 / 1.73 / 0.33 ms; 0% loss).
- RF/PHY values come from gNB log snapshots as tabulated in RUN1 `REPORT.md` Section 2.1; RSRP is `N/A`
  for phases T8/Final in the source and is stored as blank/NaN, not imputed.

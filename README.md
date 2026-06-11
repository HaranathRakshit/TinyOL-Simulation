# TinyOL: Edge Anomaly Detection and Self-Healing Simulation

[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.20641577-blue)](https://doi.org/10.5281/zenodo.20641577)
[![GitHub Pages](https://img.shields.io/badge/Live_Demo-GitHub_Pages-brightgreen)](https://haranathrakshit.github.io/TinyOL-Simulation/)

Interactive mathematical visualizer and traffic simulation dashboard for the **TinyOL** framework, an ultra-lightweight anomaly detection algorithm designed to protect multi-modal IoT sensor reliability and multimedia Quality of Experience (QoE).

**🔗 Live Interactive Dashboard:** [https://haranathrakshit.github.io/TinyOL-Simulation/](https://haranathrakshit.github.io/TinyOL-Simulation/)

---

### 📊 Hardware Dataset (Open Science)
*Note: This repository contains the interactive mathematical simulation of the TinyOL logic.* The actual physical telemetry datasets evaluated in the manuscript—captured using real **ESP32 hardware**, an **INMP441 I2S digital microphone**, and a **Raspberry Pi 5** edge server—are publicly available on Zenodo to support open science and reproducibility:
* **[Download the Raw Hardware Dataset (Zenodo)](https://doi.org/10.5281/zenodo.20641577)**

---

### About the Framework
Relying on Cloud-based processing for network anomaly detection introduces latency dependencies and severe privacy risks for smart residential monitors (e.g., streaming continuous audio to third-party servers). 

**TinyOL** addresses this by deploying an unsupervised CUSUM-EWMA statistical framework directly on ESP32-class microcontrollers. It allows the edge node to monitor physical MAC-layer router queue backpressure locally, triggering fallback mitigations without offloading private data.

This dashboard visualizes the mathematical core of the paper, demonstrating:
* **Real-time variance tracking** using Exponentially Weighted Moving Averages ($\mu, \sigma$).
* **Cumulative Danger Score accumulation** ($S_t$) using a CUSUM control chart.
* **Anti-Poisoning State Machine:** A localized feedback loop that freezes the baseline during sustained volumetric network jamming (e.g., DDoS floods) to prevent alert fatigue.
* **Extreme Memory Efficiency:** Constant $\mathcal{O}(1)$ spatial complexity requiring $< 12$ Bytes of SRAM, leaving the microcontroller free to handle AES-128-GCM encryption and multi-modal sensor fusion.

This web-based simulator implements the complete **TinyOL Unified Closed-Loop Edge Intelligence Pipeline** (CUSUM-EWMA detection + MAPE-K self-healing) as described in the accompanying research paper. It serves as a parameter exploration and validation tool.

> [!IMPORTANT]
> **Note for Reviewers:** All experimental results reported in the manuscript were captured using **physical ESP32 hardware** and a real Wi-Fi router, streaming telemetry to a **Raspberry Pi 5 (16 GB)** edge server running a Node.js Docker container on Ubuntu — not this web application.

The simulator consists of two interactive tabs:

| Tab | Module | Purpose |
|:---:|:---|:---|
| **1** | CUSUM-EWMA Detection Engine | Visualizes real-time latency monitoring, Z-score standardization, Danger Score accumulation, and the anti-poisoning state machine |
| **2** | MAPE-K Adaptive Self-Healing Simulator | Demonstrates dual-feature root-cause diagnosis (RSSI & latency), severity-aware recovery factor selection, and static vs. adaptive benchmark comparison |

---

## Tab 1: CUSUM-EWMA Detection Engine

### Algorithm Implementation

The detection engine tracks raw latency $L_t$ against an online-adapted baseline ($\mu$, $\sigma$) to compute a cumulative Danger Score $S_t$:

**Adaptive Baseline Estimation (EWMA):**
$$\mu_t = (1 - \alpha) \cdot \mu_{t-1} + \alpha \cdot L_t$$
$$\sigma_t = (1 - \alpha) \cdot \sigma_{t-1} + \alpha \cdot |L_t - \mu_t|$$

**Anomaly Accumulation (CUSUM):**
$$Z_t = \frac{L_t - \mu}{\sigma + \epsilon}$$
$$S_t = \min\left(MaxCap, \max\left(0, S_{t-1} + Z_t - k\right)\right)$$

**Anti-Poisoning State Machine:**

| State | Condition | Baseline Learning Rate |
|:---|:---|:---|
| **Normal** | $S_t < \frac{h}{3}$ | Full $\alpha$ |
| **Warning** | $\frac{h}{3} \le S_t < h$ | Halved $\frac{\alpha}{2}$ |
| **Critical** | $S_t \ge h$ | **FROZEN** (no updates) |

### Default Parameters

| Parameter | Symbol | Default | Range (Slider) | Description |
|:---|:---:|:---:|:---:|:---|
| Learning Rate | $\alpha$ | 0.05 | 0.005 – 0.200 | EWMA adaptation speed |
| Tolerance Slack | $k$ | 2.0 | 0.5 – 4.0 | CUSUM drift filter |
| Alert Threshold | $h$ | 15.0 | 5.0 – 40.0 | Danger Score alarm level |
| Maximum Cap | $MaxCap$ | 80 | 20 – 100 | Prevents unbounded score inflation |
| Epsilon | $\epsilon$ | $10^{-6}$ | Fixed | Division-by-zero guard |

### Interactive Features

- **Healthy Link:** Normal latency $\sim 0.35\text{ ms} \pm 0.04\text{ ms}$
- **Inject Jitter:** Transient burst of 20 packets at $0.85\text{--}1.20\text{ ms}$
- **INITIATE DDOS FLOOD:** Sustained volumetric attack at $1.30\text{--}2.20\text{ ms}$ (toggles to HALT ATTACK)
- **Real-time slider tuning:** Adjust all 4 hyperparameters during live simulation

---

## Tab 2: MAPE-K Adaptive Self-Healing Simulator

### Diagnostic Thresholds

| Threshold | Symbol | Value | Source |
|:---|:---:|:---:|:---|
| RSSI Threshold | $\gamma_R$ | $-65\text{ dBm}$ | Empirical baseline boundary |
| Latency Threshold | $\gamma_L$ | $350\text{ ms}$ | Empirical baseline boundary |
| Severe Latency Threshold | $\gamma_{Lh}$ | $700\text{ ms}$ | 95th-percentile calibration |
| Severe RSSI Threshold | $\gamma_{Rl}$ | $-80\text{ dBm}$ | Minimum RSSI calibration |

### State Inference Logic

```
if (RSSI <= \gamma_R) AND (Latency > \gamma_L) -> Combined
if (RSSI <= \gamma_R)                          -> Distance
if (Latency > \gamma_L)                        -> Congestion
otherwise                                       -> Baseline
```

### Adaptive Recovery Factors

| Diagnosed State | Mild Severity | Severe Severity |
|:---|:---:|:---:|
| **Congestion** | $\alpha_1 = 0.60$ (Backoff) | $\alpha_2 = 0.53$ (Channel Switch) |
| **Distance** | $\beta_1 = 0.70$ (TX Power Boost) | $\beta_2 = 0.53$ (Reconnection) |
| **Combined** | $\alpha_i \times \beta_j$ (multiplicative) | $\alpha_i \times \beta_j$ (multiplicative) |
| **Baseline** | No action (factor = 1.0) | No action (factor = 1.0) |

### Static Recovery Benchmarks (for comparison)

| State | Static Factor |
|:---|:---:|
| Congestion | 0.70 |
| Distance | 0.80 |
| Combined | $0.70 \times 0.80 = 0.56$ |

---

## Scenario Validation Matrix

All 9 scenarios have been rigorously tested. The following table confirms mathematical correctness of the simulator against the paper's theoretical formulations:

| # | Scenario | RSSI Range (dBm) | Latency Range (ms) | Diagnosed State | Severity | Proposed Factor | Static Factor | Verified |
|:---:|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 | Baseline | $[-55, -40]$ | $[150, 340]$ | Baseline | None | 1.00 | 1.00 | ✅ |
| 2 | Congestion (Mild) | $[-58, -38]$ | $[360, 650]$ | Congestion | Mild | $\alpha_1 = 0.60$ | 0.70 | ✅ |
| 3 | Congestion (Severe) | $[-60, -40]$ | $[701, 950]$ | Congestion | Severe | $\alpha_2 = 0.53$ | 0.70 | ✅ |
| 4 | Distance (Mild) | $[-79, -66]$ | $[180, 450]$ | Distance | Mild | $\beta_1 = 0.70$ | 0.80 | ✅ |
| 5 | Distance (Severe) | $[-97, -81]$ | $[200, 500]$ | Distance | Severe | $\beta_2 = 0.53$ | 0.80 | ✅ |
| 6 | Cong-Mild + Dist-Mild | $[-79, -66]$ | $[360, 650]$ | Combined | C:Mild + D:Mild | $\alpha_1 \times \beta_1 = 0.42$ | 0.56 | ✅ |
| 7 | Cong-Severe + Dist-Mild | $[-79, -66]$ | $[701, 950]$ | Combined | C:Sev + D:Mild | $\alpha_2 \times \beta_1 = 0.371$ | 0.56 | ✅ |
| 8 | Cong-Mild + Dist-Severe | $[-97, -81]$ | $[360, 650]$ | Combined | C:Mild + D:Sev | $\alpha_1 \times \beta_2 = 0.318$ | 0.56 | ✅ |
| 9 | Cong-Severe + Dist-Severe | $[-97, -81]$ | $[701, 950]$ | Combined | C:Sev + D:Sev | $\alpha_2 \times \beta_2 = 0.2809$ | 0.56 | ✅ |

> [!NOTE]
> Distance scenarios (4, 5) may occasionally classify as "Combined" when the randomly generated latency exceeds 350 ms within the specified range. This is correct behavior — weak signals often cause retransmissions that manifest as network-layer congestion.

---

## Algorithm–Paper Synchronization

A formal line-by-line audit verified **32/32 algorithmic elements** are synchronized between the simulator and the manuscript:

| Stage | Elements Checked | Synchronized |
|:---|:---:|:---:|
| Stage 1: CUSUM-EWMA Detection | 10 | 10/10 ✅ |
| Stage 2: Root-Cause Diagnosis | 12 | 12/12 ✅ |
| Stage 3: Adaptive Recovery | 10 | 10/10 ✅ |
| **Total** | **32** | **32/32 ✅** |

---

## Technical Specifications

| Metric | Value |
|:---|:---|
| **RAM footprint** | 12 Bytes ($\mathcal{O}(1)$ — 3 floating-point variables) |
| **Code size** | ~1.5 KB |
| **Execution time** | < 1 μs per packet |
| **Detection tick rate** | 150 ms (Tab 1) |
| **Healing step rate** | 1000 ms (Tab 2, autoplay) |
| **Chart history** | 60 points (Tab 1), 20 points (Tab 2) |
| **Packet log** | Last 10 entries (Tab 2) |
| **Session buffer** | Last 50 packets (Tab 2) |

---

## Dataset

The experimental datasets (55,026 real telemetry samples) used in the manuscript are publicly archived on Zenodo:

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20641577.svg)](https://doi.org/10.5281/zenodo.20641577)

---

## Citation

If you use this simulator or the associated datasets, please cite the dataset:

```bibtex
@misc{rakshit2026tinyol_dataset,
  author       = {Rakshit, Haranath and Banerjee, Subhasis},
  title        = {TinyOL: Experimental Telemetry Dataset for Closed-Loop Edge Intelligence in WSN},
  year         = {2026},
  publisher    = {Zenodo},
  doi          = {10.5281/zenodo.20641577},
  url          = {https://doi.org/10.5281/zenodo.20641577}
}
```

---

## Authors

* **Haranath Rakshit** - *Department of Computer and System Sciences, Visva-Bharati University, India*
* **Subhasis Banerjee** - *Department of Computer and System Sciences, Visva-Bharati University, India*

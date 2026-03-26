# TinyOL: Edge Anomaly Detection Simulation

Interactive mathematical visualizer and traffic simulation dashboard for the **TinyOL** framework, an ultra-lightweight anomaly detection algorithm designed to protect multi-modal IoT sensor reliability and multimedia Quality of Experience (QoE).

### 🌐 Live Interactive Dashboard
**[Click here to view the live mathematical simulation](https://haranathrakshit.github.io/TinyOL-Simulation/)**

---

### 📊 Hardware Dataset (Open Science)
*Note: This repository contains the interactive mathematical simulation of the TinyOL logic.* The actual physical telemetry datasets evaluated in the manuscript—captured using real **ESP32 hardware**, an **INMP441 I2S digital microphone**, and a **Raspberry Pi 5** edge server—are publicly available on Zenodo to support open science and reproducibility:
* **[Download the Raw Hardware Dataset (Zenodo)](https://doi.org/10.5281/zenodo.19193941)**

---

### About the Framework
Relying on Cloud-based processing for network anomaly detection introduces latency dependencies and severe privacy risks for smart residential monitors (e.g., streaming continuous audio to third-party servers). 

**TinyOL** addresses this by deploying an unsupervised CUSUM-EWMA statistical framework directly on ESP32-class microcontrollers. It allows the edge node to monitor physical MAC-layer router queue backpressure locally, triggering fallback mitigations without offloading private data.

This dashboard visualizes the mathematical core of the paper, demonstrating:
* **Real-time variance tracking** using Exponentially Weighted Moving Averages ($\mu, \sigma$).
* **Cumulative Danger Score accumulation** ($S_t$) using a CUSUM control chart.
* **Anti-Poisoning State Machine:** A localized feedback loop that freezes the baseline during sustained volumetric network jamming (e.g., DDoS floods) to prevent alert fatigue.
* **Extreme Memory Efficiency:** Constant $\mathcal{O}(1)$ spatial complexity requiring $< 12$ Bytes of SRAM, leaving the microcontroller free to handle AES-128-GCM encryption and multi-modal sensor fusion.

---

### Authors
* **Haranath Rakshit** - *Department of Computer and System Sciences, Visva-Bharati University, India*
* **Subhasis Banerjee** - *Department of Computer and System Sciences, Visva-Bharati University, India*

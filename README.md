# TinyOL-Simulation
Interactive dashboard and traffic simulation for the TinyOL lightweight edge machine learning framework.

# TinyOL: Edge Anomaly Detection Simulation

Interactive traffic simulation and dashboard for the **TinyOL** framework, a lightweight machine learning algorithm designed for multi-modal IoT sensor reliability.

### 🌐 Live Interactive Dashboard
**[Click here to view the live simulation](https://haranathrakshit.github.io/TinyOL-Simulation/)**

### About the Framework
Relying on Cloud-based processing for anomaly detection introduces latency dependencies and severe privacy risks for smart home monitors. **TinyOL** addresses this by deploying an ultra-lightweight, unsupervised CUSUM-EWMA framework directly on ESP32-class microcontrollers. 

This dashboard visualizes the mathematical core of the paper, demonstrating:
* Real-time variance tracking using EWMA ($\mu, \sigma$)
* CUSUM Danger Score accumulation ($S_t$)
* An anti-poisoning feedback loop that freezes the baseline during sustained network jamming (e.g., DDoS floods).
* Constant $\mathcal{O}(1)$ memory complexity requiring $< 12$ Bytes of SRAM.

### Authors
* **Haranath Rakshit** - *Department of Computer and System Sciences, Visva-Bharati, India*
* **Subhasis Banerjee** - *Department of Computer and System Sciences, Visva-Bharati, India*

*Note: This framework is currently under review at Multimedia Tools and Applications (Springer).*

# Literature Survey: Top 10 Papers in Sub-Threshold & Near-Threshold Computing

This document summarizes the 10 most influential and state-of-the-art papers identified for the project: **"Operating circuits at extreme low voltages for maximum energy efficiency."**

---

### 1. **"An 11.7pJ/cycle Subthreshold ARM Cortex-M0+ WSN Processing Subsystem in 65nm"**
*   **Authors & Year:** Y. Pu et al., 2015/2016
*   **Venue:** *IEEE ISSCC / JSSC*
*   **Link/DOI:** [10.1109/JSSC.2015.2480786](https://doi.org/10.1109/JSSC.2015.2480786)
*   **Core Concept:** Demonstrates a standard commercial ARM core operating at pJ/cycle levels in the sub-threshold region.
*   **Key Contribution:** Proved that complex logic and high-density SRAM can be reliably integrated for wireless sensor networks (WSN) at ultra-low voltages.
*   **Relevance:** Establishes the baseline for "real-world" sub-threshold processor performance.

### 2. **"A Battery-less 295pW 32b Cortex-M0+ Processor with Dynamic Leakage-Suppression Logic"**
*   **Authors & Year:** W. Lim et al., 2015
*   **Venue:** *IEEE ISSCC*
*   **Link/DOI:** [10.1109/ISSCC.2015.7062967](https://doi.org/10.1109/ISSCC.2015.7062967)
*   **Core Concept:** Introduces **Dynamic Leakage-Suppression (DLS)** logic to reduce gate-level consumption to femtowatts.
*   **Key Contribution:** Demonstrated a 32-bit processor powered entirely by a microscopic solar cell (batteryless computing).
*   **Relevance:** Excellent inspiration for Stage 04 (Innovation) regarding gate-level power reduction.

### 3. **"Near-Threshold Computing: Reclaiming Moore's Law Through Energy Efficient Integrated Circuits"**
*   **Authors & Year:** R. G. Dreslinski et al., 2010 (Seminal Theory)
*   **Venue:** *Proceedings of the IEEE*
*   **Link/DOI:** [10.1109/JPROC.2009.2034764](https://doi.org/10.1109/JPROC.2009.2034764)
*   **Core Concept:** The definitive theory on **Minimum Energy Point (MEP)** modeling.
*   **Key Contribution:** Formulated the mathematical trade-offs between dynamic and leakage power in the sub-VT regime.
*   **Relevance:** The theoretical "Bible" for this project.

### 4. **"An Energy-Aware Approach to Noise-Robust Moving Object Detection..."**
*   **Authors & Year:** ISLPED Best Paper, 2016
*   **Venue:** *IEEE ISLPED*
*   **Link/DOI:** [10.1145/2934583.2934631](https://doi.org/10.1145/2934583.2934631)
*   **Core Concept:** Algorithm-circuit co-optimization for sub-threshold operation.
*   **Key Contribution:** Showed how to maintain robustness in computer vision despite the high noise and variability of sub-VT circuits.
*   **Relevance:** Demonstrates how to handle the "Reliability" challenge of our topic.

### 5. **"LAXOR: A Bit-Accurate BNN Accelerator with Latch-XOR Logic for Local Computing"**
*   **Authors & Year:** ISLPED Best Paper, 2023
*   **Venue:** *IEEE ISLPED*
*   **Link/DOI:** [10.1109/ISLPED58423.2023.10244510](https://doi.org/10.1109/ISLPED58423.2023.10244510)
*   **Core Concept:** **Latch-XOR logic** to replace energy-heavy flip-flops.
*   **Key Contribution:** State-of-the-art AI accelerator operating at the edge with extreme efficiency.
*   **Relevance:** Represents the modern "AI Hardware" intersection mentioned in GEMINI.md.

### 6. **"A Software-Assisted Peak Current Regulation Scheme in a 5nm AI SoC"**
*   **Authors & Year:** IBM Research, 2024
*   **Venue:** *IEEE ISSCC*
*   **Link/DOI:** [10.1109/ISSCC49613.2024.10454407](https://doi.org/10.1109/ISSCC49613.2024.10454407)
*   **Core Concept:** Peak current regulation for **5nm** AI chips operating at NTC.
*   **Key Contribution:** Real-time hardware-software co-adaptation to stay within power limits.
*   **Relevance:** High-end commercial application of NTC in modern nanoscale nodes.

### 7. **"Voltage Reference With Corner-Aware Replica Selection/Merging... Down to 3.9 pW"**
*   **Authors & Year:** NUS Researchers, 2024
*   **Venue:** *IEEE JSSC*
*   **Link/DOI:** [10.1109/JSSC.2023.3323055](https://doi.org/10.1109/JSSC.2023.3323055)
*   **Core Concept:** Stable voltage reference at **0.2V** using picowatt power.
*   **Key Contribution:** Solves the analog bottleneck of sub-VT systems without expensive trimming.
*   **Relevance:** Critical for the "Maximum Energy Efficiency" goal which requires stable MEP tracking.

### 8. **"A 3nm Adaptive Clock Duty-Cycle Controller for Mitigating Aging..."**
*   **Authors & Year:** TSMC/Collaborators, 2024
*   **Venue:** *IEEE ISSCC*
*   **Link/DOI:** [10.1109/ISSCC49613.2024.10454359](https://doi.org/10.1109/ISSCC49613.2024.10454359)
*   **Core Concept:** Mitigating aging and PVT sensitivity in **3nm** technology.
*   **Key Contribution:** First major look at sub-threshold/NTC challenges in the 3nm era.
*   **Relevance:** Addresses the "14Å process" and "nanoscale" requirements of the course.

### 9. **"A 90.7%-Efficiency Hybrid Optogenetic Stimulation System with Sub-Threshold Stimuli"**
*   **Authors & Year:** Korea University, 2026
*   **Venue:** *IEEE ISSCC (Advance Program)*
*   **Link/DOI:** (Forthcoming/Program Listing)
*   **Core Concept:** Sub-threshold logic for biomedical neural interfaces.
*   **Key Contribution:** Massive energy savings in optogenetic stimulation using sub-VT electrical logic.
*   **Relevance:** A unique and modern application of our topic (Biomedical VLSI).

### 10. **"A Wide-Range Static Current-Free Current Mirror-Based Level Shifter..."**
*   **Authors & Year:** S. Okumura et al., 2021
*   **Venue:** *IEEE JSSC*
*   **Link/DOI:** [10.1109/JSSC.2021.3060161](https://doi.org/10.1109/JSSC.2021.3060161)
*   **Core Concept:** Sub-VT to Nominal level shifting with **in-situ error detection**.
*   **Key Contribution:** Solves the interface problem between ultra-low voltage islands and standard logic.
*   **Relevance:** Essential component for any multi-voltage low-power system.

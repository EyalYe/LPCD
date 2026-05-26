# Literature Survey: Top 5 Papers in Sub-Threshold & Near-Threshold Computing

This document summarizes the 5 most critical papers for the project: **"Operating circuits at extreme low voltages for maximum energy efficiency."**

---

### 1. **"Modeling and Sizing for Minimum Energy Operation in Sub-threshold Circuits"**
*   **Authors & Year:** B. H. Calhoun, A. Wang, and A. Chandrakasan, 2005
*   **Venue:** *IEEE Journal of Solid-State Circuits (JSSC)*
*   **Link/DOI:** [10.1109/JSSC.2005.852162](https://doi.org/10.1109/JSSC.2005.852162)
*   **Core Concept:** Analytical modeling of the **Minimum Energy Point (MEP)**.
*   **Key Contribution:** Formulated the definitive energy equation ($E_{tot} = E_{dyn} + E_{leak}$) showing the optimal $V_{DD}$ for efficiency. Proved that minimum-sized devices are generally optimal in sub-threshold.
*   **Relevance:** Foundational physics and math for the project.

### 2. **"Near-Threshold Computing: Reclaiming Moore's Law Through Energy Efficient Integrated Circuits"**
*   **Authors & Year:** R. G. Dreslinski et al., 2010
*   **Venue:** *Proceedings of the IEEE*
*   **Link/DOI:** [10.1109/JPROC.2009.2034764](https://doi.org/10.1109/JPROC.2009.2034764)
*   **Core Concept:** The transition from deep sub-threshold to **Near-Threshold Computing (NTC)**.
*   **Key Contribution:** Identified the NTC "sweet spot" where energy gains are maximized without the extreme performance penalties and variability of deep sub-threshold operation.
*   **Relevance:** The theoretical framework for modern ultra-low power systems.

### 3. **"0.45 v and 18 μA/MHz MCU SOC with Advanced Adaptive Dynamic Voltage Control (ADVC)"**
*   **Authors & Year:** U. Zangi et al., 2018
*   **Venue:** *Journal of Low Power Electronics and Applications (JLPEA)*
*   **Link/DOI:** [10.3390/jlpea8020014](https://doi.org/10.3390/jlpea8020014)
*   **Core Concept:** Silicon implementation of **Adaptive Dynamic Voltage Control (ADVC)**.
*   **Key Contribution:** Developed a holistic SOC-level approach using on-die sensors and "First Fail" (FFail) monitors to track and maintain the MEP in real-time, overcoming process and temperature variations.
*   **Relevance:** Practical implementation strategy for robust sub-threshold operation.

### 4. **"LAXOR: A Bit-Accurate BNN Accelerator with Latch-XOR Logic for Local Computing"**
*   **Authors & Year:** ISLPED Best Paper, 2023
*   **Venue:** *IEEE ISLPED*
*   **Link/DOI:** [10.1109/ISLPED58423.2023.10244510](https://doi.org/10.1109/ISLPED58423.2023.10244510)
*   **Core Concept:** Using **Latch-XOR logic** to replace energy-heavy flip-flops in sub-VT AI accelerators.
*   **Key Contribution:** Demonstrated a high-efficiency Binary Neural Network (BNN) accelerator designed specifically for the edge, minimizing the switching activity and static leakage.
*   **Relevance:** Modern "Industry Use Case" for Edge AI hardware.

### 5. **"A Software-Assisted Peak Current Regulation Scheme in a 5nm AI SoC"**
*   **Authors & Year:** IBM Research, 2024
*   **Venue:** *IEEE ISSCC*
*   **Link/DOI:** [10.1109/ISSCC49613.2024.10454407](https://doi.org/10.1109/ISSCC49613.2024.10454407)
*   **Core Concept:** Managing Near-Threshold operation in **5nm nanoscale nodes**.
*   **Key Contribution:** Introduced real-time hardware-software co-adaptation to regulate peak current and power delivery in highly dense, advanced technology nodes.
*   **Relevance:** Addresses the "14Å" and "Nanoscale" course requirements with current SOTA research.

# Presentation Outline: Extreme Low Voltage Computing

## 1. Introduction: Beyond Traditional Power Savings
- **Context**: Acknowledge existing knowledge of clock gating, power gating, and standard DVFS.
- **The Next Frontier**: Why we need to move from "saving power" to "maximizing energy efficiency."

## 2. Motivation: The "Why" of Extreme Low Voltage
- **The Data Explosion**: IoT, wearables, and the need for "Always-On" sensing.
- **The Edge AI Challenge**: Moving intelligence from the cloud to the device under microwatt constraints.
- **Nanoscale Limits (14Å)**: Why traditional voltage scaling is no longer enough to manage heat and battery life.

## 3. Theoretical Foundation
- **Defining the Regions**:
    - **Sub-threshold ($V_{DD} < V_{th}$)**: Relying on diffusion current ($I_{sub} \propto e^{(V_{GS}-V_{th})/nV_T}$).
    - **Near-threshold ($V_{DD} \approx V_{th}$)**: The "sweet spot" for energy vs. performance.
- **Minimum Energy Point (MEP) Theory (Calhoun et al., 2005)**:
    - **Dynamic Energy**: Decreases quadratically with $V_{DD}$.
    - **Leakage Energy**: Increases exponentially with lower $V_{DD}$ due to increased delay.
    - **The MEP**: The optimal $V_{DD}$ where $E_{dyn} + E_{leak}$ is minimized.
- **Sizing for Energy**: Why minimum width ($W_{min}$) is often optimal in sub-VT.

## 3. Advanced Techniques: Overcoming the Challenges
- **The Challenges**: 
    - Extreme sensitivity to PVT (Process, Voltage, Temperature) variations.
    - Reduced noise margins and "First Fail" risks.
- **Adaptive Dynamic Voltage Control (ADVC) (Zangi et al., 2018)**:
    - **Holistic Approach**: Using on-die sensors (Process, Temp, Aging, $V_{th}$).
    - **Real-time MEP Tracking**: Dynamically adjusting voltage to stay at the optimal energy point.
    - **First Fail (FFail) Monitor**: Programmable critical path monitoring to detect functional limits.

## 4. Practical & Future Industry Use Cases
- **Edge AI & BNN Accelerators**: Implementing Latch-XOR logic for ultra-low power machine learning.
- **Batteryless Systems**: Powering processors from microscopic solar cells or energy harvesters.
- **Biomedical VLSI**: Sub-threshold circuits for neural interfaces and implantable devices.
- **The 3nm/14Å Frontier**: Managing aging and variability in the latest nanoscale technologies.

## 5. Conclusion & Transition
- Summary of the paradigm shift from performance-first to energy-first design.
- Leading into our proposed innovation (Stage 04).

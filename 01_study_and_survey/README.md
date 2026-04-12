# 01 Study and Survey (20%)

## Objective
A deep study and literature survey of **Operating circuits at extreme low voltages for maximum energy efficiency**.

## Focus Areas
- [x] **Near-Threshold and Sub-Threshold Computing**: The physics of $V_{DD} < V_{th}$.
- [x] **Energy-Efficiency Metrics**: Power Delay Product (PDP), Energy-Delay Product (EDP), and Minimum Energy Point (MEP).
- [ ] **Reliability & Variability**: Impact of Process, Voltage, and Temperature (PVT) variations on sub-threshold operation.

## Literature Survey
| Paper Title | Authors | Key Contribution | Relevancy |
|-------------|---------|------------------|-----------|
| **An 11.7pJ/cycle Subthreshold ARM Cortex-M0+...** | Y. Pu et al. (2016) | Commercial ARM core in sub-threshold | Baseline for "real-world" processor performance. |
| **A Battery-less 295pW 32b Cortex-M0+ Processor...** | W. Lim et al. (2015) | Dynamic Leakage-Suppression (DLS) logic | Inspiration for gate-level power reduction. |
| **Near-Threshold Computing: Reclaiming Moore's Law...** | Dreslinski et al. (2010) | MEP mathematical modeling | Foundational theory/Bible for the project. |
| **An Energy-Aware Approach to Noise-Robust Moving Object...** | ISLPED Best Paper (2016) | Algorithm-circuit co-optimization | Reliability handling in sub-VT circuits. |
| **LAXOR: A Bit-Accurate BNN Accelerator...** | ISLPED Best Paper (2023) | Latch-XOR logic innovation | Modern SOTA for AI at the edge. |
| **A Software-Assisted Peak Current Regulation Scheme...** | IBM Research (2024) | HW-SW co-adaptation in 5nm | NTC implementation in modern nanoscale nodes. |
| **Voltage Reference With Corner-Aware Replica...** | NUS Researchers (2024) | 0.2V stable reference at pW levels | Critical for stable MEP tracking. |
| **A 3nm Adaptive Clock Duty-Cycle Controller...** | TSMC/Collab. (2024) | Mitigation of aging/PVT in 3nm | Addressing 3nm/14Å process challenges. |
| **A 90.7%-Efficiency Hybrid Optogenetic Stimulation...** | Korea Univ. (2026) | Sub-VT for biomedical neural interfaces | Modern/unique application of the topic. |
| **A Wide-Range Static Current-Free Level Shifter...** | S. Okumura et al. (2021) | Sub-VT to nominal level shifting | Essential multi-voltage system component. |

## Deliverables
- [x] Comprehensive literature survey of at least 10 state-of-the-art papers.
- [ ] Research notes on foundational physics and current challenges.

## AI Tools
- [x] Gemini for summarizing key findings.
- [ ] NotebookLM for deep paper analysis.

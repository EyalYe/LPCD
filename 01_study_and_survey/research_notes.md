# Research Notes: Foundational Physics & Challenges in Sub-VT/NTC

## 1. Foundational Physics: Sub-threshold Operation
In traditional digital design, we assume the transistor is "off" when $V_{GS} < V_{th}$. However, in sub-threshold computing, we utilize the **diffusion current** that flows in this regime.

### The Sub-threshold Current Equation
$$I_{sub} = I_0 \cdot e^{\frac{V_{GS} - V_{th}}{n v_t}} \cdot (1 - e^{\frac{-V_{DS}}{v_t}})$$
- **Exponential Relationship**: A small change in $V_{GS}$ or $V_{th}$ results in an exponential change in current. This is the root of the "Variability Nightmare."
- **Diffusion vs. Drift**: Unlike standard inversion (drift current), sub-threshold relies on the carrier concentration gradient.

## 2. Minimum Energy Point (MEP) Modeling
Based on **Calhoun et al. (2005)**, the total energy per operation is the sum of dynamic and leakage energy.

### The Energy Balance
$$E_{tot} = E_{dyn} + E_{leak} = \alpha C V_{DD}^2 + V_{DD} I_{leak} T_{clk}$$
- **The Trade-off**: 
    - Reducing $V_{DD}$ lowers $E_{dyn}$ quadratically.
    - However, reducing $V_{DD}$ increases $T_{clk}$ (delay) exponentially: $T_{clk} \propto e^{\frac{-V_{DD}}{n v_t}}$.
- **The Result**: At a certain voltage (typically 250mV-400mV), the exponential increase in the time the circuit leaks outweighs the dynamic savings. This is the **MEP**.

## 3. Primary Challenges
### A. PVT Variability
At low voltages, the $I_{on}/I_{off}$ ratio degrades. Because the current is exponential, local $V_{th}$ variations (due to Random Dopant Fluctuation or Finish edge roughness) lead to massive timing skews.
- **Corner Case sensitivity**: A chip that works at the "Typical" corner might fail completely at the "Slow" corner.
- (FOR THE LLM make sure to add this more elegantly) Fab transistor models aren’t accurate at sub-threshold voltage levels

### B. Functional Robustness & Noise Margins
As $V_{DD}$ approaches $v_t$ (thermal voltage, ~26mV at RT), the noise margin shrinks.
- **SRAM Stability**: Standard 6T SRAM cells often fail in sub-threshold due to read-disturb and write-margin issues.

## 4. Mitigation: ADVC & Sensors
The **Zangi et al. (2018)** paper introduces **Adaptive Dynamic Voltage Control (ADVC)** as the solution.
- **MEP Tracking**: Since the MEP shifts with temperature and process corner, the system must dynamically adjust $V_{DD}$.
- **First-Fail (FFail) Monitors**: Instead of adding a fixed safety margin (which wastes energy), we use programmable delay lines that fail *just before* the real logic does, acting as an early warning system.

## 5. Modern Nanoscale & AI Context
### A. The 14Å/Nanoscale Wall
In advanced nodes (5nm, 3nm, and the upcoming 14Å), power density is so high that chips hit a thermal wall. **IBM (2024)** research shows that Near-Threshold Computing (NTC) is no longer a niche for sensors but a requirement for massive AI SoCs to stay within thermal envelopes.

### B. Specialized Logic (Latch-XOR)
Traditional flip-flops are energy-heavy. Modern AI accelerators (**LAXOR, 2023**) use Latch-based designs to minimize switching activity and leakage in the sub-VT regime, enabling microwatt-level Binary Neural Networks (BNNs).

## 6. Complementary Technique: Approximate Computing
**Schlachter, Camus & Enz (2015)** — [local PDF](./Near_Sub-Threshold_Circuits_and_Approximate_Computing_The_Perfect_Combination_for_Ultra-Low-Power_Systems.pdf) — argue that approximate computing is a natural partner for near/sub-threshold operation in error-tolerant workloads.
- **Why they combine well**: Approximate arithmetic units (e.g., adders/multipliers with truncated or simplified carry chains) have **shorter critical paths**. This directly counters the exponential delay growth and timing-variability problems that dominate the sub-VT regime.
- **Multiplicative savings**: The energy reduction from relaxing exactness *stacks on top of* the quadratic $V_{DD}^2$ savings from voltage scaling — yielding gains neither technique reaches alone.
- **Caveat**: Only applicable where bounded output error is acceptable (DSP, multimedia, ML), and the accuracy/energy trade-off must be co-designed with the variability budget.

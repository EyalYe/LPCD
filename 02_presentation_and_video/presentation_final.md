# Final Presentation: Operating Circuits at Extreme Low Voltages
## Class Lecture — 45 Minutes (33 min slides + 12 min Kahoot)

---

## Timing Overview

| Block | Presenter | Slides | Duration |
|-------|-----------|--------|----------|
| Intro & Motivation | Presenter 1 | 1–3 | 0:00 – 10:00 |
| Physics & MEP | Presenter 2 | 4–7 | 10:00 – 23:00 |
| ADVC, Industry & Conclusion | Presenter 3 | 8–11 | 23:00 – 33:00 |
| Kahoot | Presenter 3 hosts | — | 33:00 – 45:00 |

---

---

## PRESENTER 1 — Intro & Physics Foundations (0:00–10:00)

---

### Slide 1: Title Slide `[0:00–1:00]`

**Title:** Operating Circuits at Extreme Low Voltages  
**Subtitle:** Foundations, Adaptive Control, and Industry Applications  
**Presenters:** [Name 1] · [Name 2] · [Name 3]

**Speaker Note:** Brief welcome. Tell the class: "By the end of this lecture, you will understand why operating below the threshold voltage is not a compromise — it is the only way forward for the next generation of chips. We will cover the physics, the math, the silicon solution, and where industry is taking this today."

---

### Slide 2: Why Are We Here? `[1:00–5:00]`

**Visual:** Three-column layout:
- Column 1 — IoT sensor node with solar panel (microwatt budget)
- Column 2 — Brain implant schematic (no battery replacement possible)
- Column 3 — Dense 5nm AI SoC cross-section (thermal wall)

**Content:**
- We have studied clock gating, power gating, and DVFS. Those are the tools of reactive power management — you shut things down when you don't need them.
- Today's challenge is different: **you can't shut down a biosensor in your brain**. You can't recharge a sensor buried in a bridge pillar. You can't run a 5nm AI chip at 1.1V without hitting the thermal envelope of the package.
- The solution is **proactive energy minimization** — finding the absolute physical minimum energy required to perform one operation on silicon.
- This is what sub-threshold and near-threshold computing is about.

**Killer Fact:** At the 14Å node, near-threshold computing is no longer a niche battery-saving trick — it is a **thermal necessity**. Without it, the power density of dense AI SoCs would exceed the thermal limits of the package.

**Q&A Prompt:** *Why not just use aggressive power gating instead?*  
→ Power gating turns the system completely off. Sub-threshold computing enables continuous always-on operation — at the absolute physical minimum energy per operation.

---

### Slide 3: The Operating Regions `[5:00–10:00]`

**Visual:** $I_D$ vs $V_{GS}$ on a **log scale**, annotated with three regions: Super-threshold, Near-threshold, Sub-threshold. Mark $V_{th}$ clearly. Add a second x-axis showing approximate energy efficiency vs. performance in each region.

**Content — The Three Regimes:**

| Regime | $V_{DD}$ | Current Type | Speed | Energy |
|--------|----------|-------------|-------|--------|
| Super-threshold | $V_{DD} \gg V_{th}$ | Drift (quadratic) | GHz | High |
| Near-threshold (NTC) | $V_{DD} \approx V_{th}$ | Mixed | 10–100 MHz | ~10× better |
| Sub-threshold | $V_{DD} < V_{th}$ | Diffusion (exponential) | kHz–MHz | ~20× better |

**The sub-threshold current equation:**
$$I_{sub} = I_0 \cdot e^{\frac{V_{GS} - V_{th}}{n V_T}} \cdot \left(1 - e^{\frac{-V_{DS}}{V_T}}\right)$$

- The exponential nature means **every millivolt counts twice as much** as in standard digital logic.
- This exponential sensitivity is both the source of the efficiency gain and the root of the variability problem.

**The NTC Sweet Spot (Dreslinski et al., 2010):**
- NTC offers ~**10× energy improvement** over nominal with ~**10× performance degradation**.
- Going deeper into sub-VT only gains ~2× more energy but loses another 50–100× in speed.
- Practical conclusion: **NTC is the optimal operating point** for most applications. Deep sub-VT is reserved for ultra-low duty-cycle sensing.

**Killer Fact:** The "10×/10×" rule. Scaling from 1.1V down to 0.5V (NTC) gives 10× energy efficiency at 10× the performance penalty. Going further to 0.25V only adds 2× more energy savings but causes 100× more slowdown.

---

---

## PRESENTER 2 — MEP Theory, Variability & ADVC (10:00–23:00)

---

### Slide 4: The Minimum Energy Point (MEP) `[10:00–15:00]`

**Visual:** Classic U-shaped energy curve. Three labeled lines:
- $E_{dyn}$ — decreasing parabola
- $E_{leak}$ — U-shaped (rises sharply at low $V_{DD}$)
- $E_{tot}$ — sum, with a clear minimum marked as "MEP (~300mV)"

**Content:**

The total energy per operation is the sum of dynamic and leakage energy:
$$E_{tot} = \underbrace{\alpha C_{tot} V_{DD}^2}_{E_{dyn}} + \underbrace{V_{DD} \cdot I_{leak} \cdot T_{clk}}_{E_{leak}}$$

**The trade-off mechanism:**
- Lowering $V_{DD}$ reduces $E_{dyn}$ quadratically — good.
- But lowering $V_{DD}$ slows the circuit **exponentially**: $T_{clk} \propto e^{-V_{DD}/nV_T}$
- At some voltage, the circuit takes so long to complete that the total leakage energy $I_{leak} \cdot T_{clk}$ exceeds what was saved dynamically.
- That crossover is the **MEP** — measured at ~**300mV in 180nm** (Calhoun et al., 2005).

**Key Conclusion:**  
The MEP is never at 0V. If you operate **below** the MEP, you are wasting energy.

**Killer Fact:** Operating below the MEP (e.g., at 100mV instead of 300mV) actively wastes energy. The exponential delay increase causes the leakage term to dominate completely.

**Q&A Prompt:** *Does the MEP stay fixed for a given chip?*  
→ No. It shifts dynamically with temperature, process corner, and aging. This is why static design-time margins fail — and why we need ADVC (Slide 7).

---

### Slide 5: Sizing for Energy — "Small is Beautiful" `[15:00–18:00]`

**Visual:** Side-by-side: standard sized inverter vs. minimum-sized inverter. Bar chart comparing $E_{dyn}$, $E_{leak}$, and $T_{clk}$ for $W = W_{min}$ vs $W = 4W_{min}$.

**Content (Calhoun et al., 2005):**

In standard digital design, we upsize gates to improve speed. **In sub-threshold, this backfires:**

- Increasing width increases capacitance → higher $E_{dyn}$.
- Increasing width increases leakage footprint → higher $E_{leak}$.
- The speed improvement from upsizing in sub-VT is **sub-linear** — it cannot offset both costs simultaneously.

**Conclusion:** $W_{min}$, $L_{min}$ is the **optimal sizing strategy** for sub-threshold circuits. Every transistor is minimum size.

**Important caveat for nanoscale:** In FinFET and GAAFET nodes (5nm, 3nm, 14Å), transistor width is **quantized** (you can only add fins). The continuous $W_{min}$ rule from Calhoun's 180nm model may need revalidation — this is an open research gap.

**Q&A Prompt:** *Doesn't minimum sizing hurt the $I_{on}/I_{off}$ ratio?*  
→ $I_{on}$ is already severely degraded in sub-VT. Adding width raises both $E_{dyn}$ and $E_{leak}$ without a proportional speedup, so the ratio of energy wasted to speed gained is always unfavorable.

---

### Slide 6: The Variability Nightmare `[18:00–22:00]`

**Visual — Two sub-panels:**
1. Gaussian distribution of $V_{th}$ across a die, showing the "Slow" and "Fast" tails that cause 2–3× timing spread.
2. SRAM cell (6T) with "read disturb" and "write margin" arrows collapsing at low $V_{DD}$.

**Content — Part A: Logic Variability**

Because $I_{sub} \propto e^{(V_{GS}-V_{th})/nV_T}$, the exponential law amplifies any variation in $V_{th}$:
- A **10mV local shift in $V_{th}$** (from Random Dopant Fluctuation or fin-edge roughness) causes a **2–3× change in path delay**.
- At the "Slow" process corner, a chip designed for 300mV MEP may not function at all.
- Traditional worst-case margin design would push the operating voltage up by 100–200mV — erasing the entire energy benefit.

**Content — Part B: SRAM Failure (Critical Gap)**

Standard **6T SRAM cells fail in sub-threshold before the logic does** due to two mechanisms:
- **Read Disturb:** During a read, the bitline voltage rise flips the storage node — the SNM (Static Noise Margin) at sub-VT is near zero.
- **Write Margin:** The cell cannot be written reliably when $V_{DD}$ falls below ~400mV.

This means: in a real system, **memory stability — not logic delay — often dictates the MEP lower bound**.

**Killer Fact:** In sub-threshold, it is the SRAM, not the logic, that usually fails first. The 6T SRAM read SNM collapses before logic paths violate timing.

**Q&A Prompt:** *Can't we just design with worst-case margins?*  
→ Yes, but that pushes $V_{DD}$ so far above the MEP that you eliminate the energy benefit entirely. You need an adaptive system that tracks variation in real-time.

---

### Slide 7: ADVC — The Silicon Solution `[22:00–26:00]`

**Visual:** Block diagram from Zangi et al. (2018) — include: FFail delay line, comparator, PMIC feedback, on-die temp/process sensors, digital control logic. Draw a closed-loop arrow.

**Content (Zangi et al., 2018 — 40nm TSMC):**

**The core insight:** Instead of designing for the worst-case corner at chip tape-out, design for the typical corner and *adapt in real-time*.

**Adaptive Dynamic Voltage Control (ADVC) Architecture:**
1. **On-Die Sensors:** Monitor temperature, process corner ($V_{th}$ deviation), and aging continuously.
2. **First-Fail (FFail) Monitor:** A programmable delay chain, tuned to fail *just before* the actual critical logic path fails — it acts as a hardware tripwire that fires before any functional error occurs.
3. **Closed-Loop Control:** When FFail triggers, the PMIC increments $V_{DD}$ by a small step. When the circuit recovers, voltage is gradually reduced back toward the MEP.

**Silicon Results:**
- Operating range: **0.45V – 1.1V** (dynamic, real-time)
- Active power: **18 μA/MHz** at 0.45V
- **3–5× more efficient** than ARM Cortex-M4F at equivalent workload
- Variation tolerance: operates correctly across all PVT corners without a fixed guard-band

**The analogy:** ADVC is cruise control for energy efficiency — it holds you at the MEP regardless of the road (temperature, process corner, aging).

**Killer Fact:** Zangi's FFail monitor eliminates the static guard-band entirely. Instead of designing for the worst case, you design for the typical case and let the hardware adapt in microseconds.

---

---

## PRESENTER 3 — Applications, Industry SOTA & Conclusion (23:00–33:00)

---

### Slide 8: Edge AI — The LAXOR Accelerator `[26:00–29:00]`

**Visual:** Architecture diagram — Latch-XOR array with weights stored in latches, XOR gates fused at outputs, popcount adder tree. Annotate with "flip-flop eliminated" callout.

**Content (LAXOR, ISLPED Best Paper 2023 — 28nm):**

**The Problem with Standard AI Hardware in Sub-VT:**
- Binary Neural Networks (BNNs) reduce multiply-accumulate to XNOR + popcount.
- But even BNN accelerators normally use heavy flip-flops for weight storage and result accumulation.
- Flip-flops in sub-VT have high setup/hold sensitivity and switch unnecessarily on every clock edge.

**The LAXOR Solution — Latch-XOR Logic:**
- **Weights stored in latches** (not flip-flops): latches use ~60% less area and consume far less switching energy.
- **XOR gate fused directly to latch output**: the XNOR computation happens *during* the latch clock transition — no separate logic stage needed.
- **Optimized adder tree**: 1.37× energy-delay savings over standard binary adder trees.

**Results:**
- Peak efficiency: **2315 TOPS/W** at sub-VT supply
- CIFAR-10 accuracy: **85.25%** — bit-accurate, fully deterministic
- Outperforms analog Compute-in-Memory (CiM) approaches in **reliability**, since there is no analog noise at the decision boundary.

**Why BNNs specifically?** XNOR operations are naturally resilient to reduced noise margins, and the 1-bit weight representation removes the precision sensitivity that makes analog CiM fragile.

**Killer Fact:** LAXOR achieves 2315 TOPS/W by eliminating the flip-flop entirely — the most energy-wasteful element in a sub-VT digital design.

---

### Slide 9: Nanoscale SOTA — IBM 5nm AI SoC `[29:00–32:00]`

**Visual:** Left side — cross-section of Nano-Sheet (GAAFET) vs. FinFET showing gate-all-around geometry. Right side — dual-loop regulation diagram (feed-forward compiler path + hardware feedback sensor path).

**Content (IBM Research, ISSCC 2024 — 5nm GAAFET):**

**Why NTC at 5nm is a Requirement, Not a Choice:**
- At 5nm density, the chip hits its thermal wall before its frequency limit.
- Running all compute blocks at nominal voltage (1.1V) would melt the package.
- NTC is mandatory for large AI SoCs — this is the shift Dreslinski predicted in 2010 finally arriving in silicon.

**The 5nm Technology Foundation:**
- **Nano-Sheet FETs (GAAFET):** Gate wraps around all four sides of the channel — better drive current control at low voltages vs. FinFET.
- **Multi-Voltage Domain Strategy:**
  - Compute-heavy blocks: **0.55V** (NTC)
  - Timing-critical paths: **0.75V** (near-nominal)

**The Dual-Loop Peak Current Regulation:**
1. **Software Feed-Forward:** Compiler analyzes the upcoming instruction stream and predicts high-current bursts (matrix multiply, attention heads). It proactively throttles frequency *before* the burst, preventing the peak from forming.
2. **Hardware Feedback:** Microsecond-granularity on-die current sensors enforce hard limits if the prediction misses, capping any unforeseen peaks before the PDN is stressed (75W PCIe limit).

**Result:** **41% improvement** in BERT-Large inference throughput through active NTC management — without exceeding thermal or power delivery budgets.

**The Open Gap:** There is no equivalent of Zangi's ADVC operating natively in a GAAFET 5nm environment. This is the research opportunity for Phase 04.

**Killer Fact:** IBM's 5nm SoC uses NTC not to save battery life, but because it would physically overheat at full voltage. NTC has become a thermal management strategy at the frontier.

---

### Slide 10: Summary & Innovation Bridge `[32:00–33:00]`

**Visual:** Timeline arrow from Calhoun 2005 → Dreslinski 2010 → Zangi 2018 → LAXOR 2023 → IBM 2024 → "Phase 04: Our Proposal"

**The Three Pillars:**
| Pillar | The Insight |
|--------|------------|
| **MEP is the goal** | There is an optimal $V_{DD}$ — find it analytically, target it physically |
| **Variation is the enemy** | Exponential sensitivity to $V_{th}$ destroys the benefit without closed-loop control |
| **Adaptation is the tool** | ADVC, FFail, software co-regulation — all converge on the same principle: track the MEP dynamically |

**The Three Open Gaps → Our Innovation (Phase 04):**
1. **GAAFET MEP Tracking:** Calhoun's $W_{min}$ rule and Zangi's FFail were built for planar/FinFET nodes. Width quantization in 5nm GAAfets may invalidate both.
2. **AI-Aware Fault Tolerance:** A framework for graceful accuracy degradation in BNNs under NTC stress — allowing soft BNN errors instead of hard timing failures.
3. **Memory-Independent ADVC:** A dedicated ADVC control loop for SRAM macros using read-disturb monitors, decoupled from the logic FFail path.

**Speaker Note:** "The literature from 2005 to 2024 builds a coherent story. Each paper solves the problem the previous one exposed. Calhoun defined the target. Dreslinski told us where to operate. Zangi showed us how to hold the target in real silicon. LAXOR brought it to AI hardware. IBM brought it to production nanoscale. The question for Phase 04 is: what does the next paper in this sequence need to solve?"

---

---

## KAHOOT — Presenter 3 Hosts `[33:00–45:00]`

**Setup:** Launch Kahoot, share pin. 6 questions, 90 seconds each (includes answer reveal and 20-second discussion). Total: ~12 minutes.

---

### Question 1
**"According to Calhoun et al. (2005), what is the typical voltage range of the Minimum Energy Point (MEP) in a standard CMOS process?"**
- A) 50–100 mV
- B) 250–400 mV ✓
- C) 600–800 mV
- D) 1.0–1.2 V

*After reveal:* "Below the MEP, you're actually wasting energy. The leakage term dominates because the circuit takes exponentially longer to finish."

---

### Question 2
**"Calhoun's sizing rule for sub-threshold circuits is the opposite of standard design. What is the correct rule?"**
- A) Upsize everything for speed
- B) Size for the worst-case PVT corner
- C) Use minimum width and length ($W_{min}$, $L_{min}$) ✓
- D) Double the width to reduce threshold sensitivity

*After reveal:* "Upsizing in sub-VT raises both capacitance and leakage without a proportional speedup — it's always a losing trade."

---

### Question 3
**"According to Dreslinski et al. (2010), operating at Near-Threshold (NTC) vs. nominal voltage gives approximately:"**
- A) 100× energy improvement, 2× performance loss
- B) 10× energy improvement, 10× performance loss ✓
- C) 2× energy improvement, 50× performance loss
- D) 5× energy improvement, no performance loss

*After reveal:* "The '10×/10×' rule. And going deeper to 250mV only adds another 2× energy — but 100× more slowdown. NTC is the practical sweet spot."

---

### Question 4
**"In Zangi's ADVC system, what is the role of the 'First-Fail (FFail)' monitor?"**
- A) It permanently disables failing logic paths
- B) It reports errors to the operating system for software correction
- C) It is a programmable delay path that trips just before real logic fails, providing an early warning to increase $V_{DD}$ ✓
- D) It measures SRAM read disturb margins

*After reveal:* "FFail is a hardware tripwire. It fires before any functional error — so the voltage is boosted proactively, not reactively after a fault."

---

### Question 5
**"Which component of a standard digital design fails FIRST when $V_{DD}$ is reduced into the sub-threshold range?"**
- A) Combinational logic gates
- B) Clock distribution networks
- C) Standard 6T SRAM cells ✓
- D) Phase-locked loops (PLLs)

*After reveal:* "SRAM read SNM collapses before logic timing fails. This means memory stability — not logic delay — often sets the real lower bound on $V_{DD}$."

---

### Question 6
**"IBM's 2024 5nm AI SoC achieved a 41% improvement in BERT-Large throughput using which technique?"**
- A) Increasing the clock frequency beyond the datasheet limit
- B) Moving all logic to a deep sub-threshold supply (200mV)
- C) Software-assisted feed-forward prediction combined with hardware current feedback in the NTC regime ✓
- D) Replacing all SRAM with eDRAM macros

*After reveal:* "The compiler predicts current bursts before they happen and throttles proactively. This is the hardware-software co-design approach that makes NTC practical at the 5nm frontier."

---

## Final Timing Check

| Section | Start | End | Duration |
|---------|-------|-----|----------|
| Slide 1: Title | 0:00 | 1:00 | 1 min |
| Slide 2: Motivation | 1:00 | 5:00 | 4 min |
| Slide 3: Operating Regions | 5:00 | 10:00 | 5 min |
| **→ Presenter 2 takes over** | | | |
| Slide 4: MEP Theory | 10:00 | 15:00 | 5 min |
| Slide 5: Sizing Rules | 15:00 | 18:00 | 3 min |
| Slide 6: Variability + SRAM | 18:00 | 23:00 | 5 min |
| Slide 7: ADVC | 23:00 | 26:00 | 3 min |
| **→ Presenter 3 takes over** | | | |
| Slide 8: LAXOR | 26:00 | 29:00 | 3 min |
| Slide 9: IBM 5nm | 29:00 | 32:00 | 3 min |
| Slide 10: Conclusion | 32:00 | 33:00 | 1 min |
| **→ Kahoot** | | | |
| 6 questions × 2 min | 33:00 | 45:00 | 12 min |
| **TOTAL** | | | **45 min** |

# Speaker 4: Applications & Conclusion
## Slides 8–10 | 26:00–33:00 (7 min) | Kahoot Q6

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 8 | Edge AI — The LAXOR Accelerator | 26:00–29:00 |
| Slide 9 | Nanoscale SOTA — IBM 5nm AI SoC | 29:00–32:00 |
| Slide 10 | Summary & Innovation Bridge | 32:00–33:00 |
| Kahoot Q6 | IBM 5nm BERT-Large technique | after section |

---

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

## Kahoot — Speaker 4 Hosts `[after section, ~2 min]`

**Setup:** Launch Kahoot, share pin for Q6. 90 seconds (includes answer reveal and 20-second discussion).

---

### Question 6
**"IBM's 2024 5nm AI SoC achieved a 41% improvement in BERT-Large throughput using which technique?"**
- A) Increasing the clock frequency beyond the datasheet limit
- B) Moving all logic to a deep sub-threshold supply (200mV)
- C) Software-assisted feed-forward prediction combined with hardware current feedback in the NTC regime ✓
- D) Replacing all SRAM with eDRAM macros

*After reveal:* "The compiler predicts current bursts before they happen and throttles proactively. This is the hardware-software co-design approach that makes NTC practical at the 5nm frontier."

---

**End of presentation.**

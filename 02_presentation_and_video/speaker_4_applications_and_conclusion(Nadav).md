# Speaker 4 (Nadav): Applications & Conclusion
## Slides 15–18 | 26:00–33:00 (7 min)

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 15 | Why BNNs at the Edge? | 26:00–27:30 |
| Slide 16 | LAXOR Architecture & Results | 27:30–29:30 |
| Slide 17 | IBM 5nm — The Production Frontier | 29:30–31:30 |
| Slide 18 | The Research Roadmap & Phase 04 | 31:30–33:00 |

---

---

### Slide 15: Why BNNs at the Edge? `[26:00–27:30]`

**Visual:** Funnel diagram: "Full-precision DNN (32-bit floats)" → "Int8 quantization" → "Binary Neural Network (1-bit)" — each step showing the energy/accuracy trade-off narrowing. On the right, a small diagram showing XNOR replacing multiply and popcount replacing accumulate.

**Content:**

Eli covered the variability and ADVC solution at the silicon level. My section is about what those tools enable at the system level — starting with the most energy-constrained computing application of the decade: **Edge AI inference**.

**The Edge AI energy problem:**

Running a neural network on a cloud server is easy — you have kilowatts of cooling and hundreds of watts of power. Running inference on an IoT sensor, a wearable, or a drone means working within a **microwatt to milliwatt budget**, often from a harvested energy source with no battery.

Full-precision DNNs are completely impractical at this scale. Even Int8 quantization (the standard on mobile hardware) is too expensive for deep sub-threshold operation because:
- 8-bit multipliers are large and power-hungry circuits.
- Multiply-accumulate units have deep pipelines with many flip-flop stages.
- Both are highly sensitive to $V_{th}$ variation at sub-VT supply — timing errors corrupt results.

**Why Binary Neural Networks (BNNs) are the natural fit:**

BNNs quantize both weights and activations to ±1 (one bit). This reduces multiply-accumulate to:
- **XNOR** (1-bit multiply): a single logic gate, not a multi-bit multiplier.
- **Popcount** (binary accumulate): count the number of matching bits, implemented as an adder tree.

XNOR gates and adder trees are simple, minimum-sized digital logic — exactly the type of circuit that performs best at sub-threshold. The 1-bit quantization also makes the computation **deterministic and noise-immune**: there is no analog precision to degrade, only a binary threshold that either holds or doesn't.

This is the starting point for LAXOR.

---

---

### Slide 16: LAXOR Architecture & Results `[27:30–29:30]`

**Visual:** Detailed architecture diagram:
- Left: Standard BNN accelerator — separate FF register bank + XOR logic stage. Annotate clock tree power and unnecessary toggling.
- Right: LAXOR — Latch-XOR cell where the XOR gate output is driven directly by the latch internal node during the enable pulse. Show the fused latch+XOR cell schematic. Below both: popcount adder tree comparison (standard vs. LAXOR optimized).

**Content (LAXOR, ISLPED Best Paper 2023 — 28nm):**

The problem LAXOR solves is specific to sub-threshold digital design: **flip-flops are the most energy-wasteful element in the entire circuit**.

**Why flip-flops are so costly in sub-threshold:**

A standard D flip-flop has three internal stages (master latch, slave latch, output buffer) plus the clock tree driving it. Even when the data input $D$ doesn't change, the clock edge toggles internal nodes — consuming switching energy every single cycle. At sub-VT voltages, flip-flop setup/hold windows become extremely sensitive to $V_{th}$ variation, requiring them to be upsized or given extra timing margin. Both raise energy.

In a conventional BNN accelerator, the weights are stored in a register file of flip-flops. Each weight read involves clocking that register — burning energy whether or not the weight changes (which, during inference, it never does).

**The Latch-XOR (LAXOR) cell:**

The key insight: a **latch** (level-sensitive storage) consumes energy only when its enable signal is asserted *and* the data changes. When the stored weight is static, a latch draws near-zero switching power — far less than a flip-flop.

LAXOR takes this further by **fusing the XOR gate directly onto the latch output node**. The XNOR computation between the stored weight and the input activation happens *during* the latch enable transition — no separate combinational stage, no extra register stage, no pipeline register between storage and compute. This is compute-in-storage at the digital level.

**The optimized popcount tree:**

Standard popcount for BNN accumulation uses carry-ripple adder trees that are not optimized for the 1-bit input distribution. LAXOR uses a custom tree structure achieving **1.37× energy-delay product improvement** over the standard approach.

**Silicon Results (28nm):**

| Metric | Value |
|--------|-------|
| Peak energy efficiency | **2315 TOPS/W** at sub-VT supply |
| Task accuracy (CIFAR-10) | **85.25%** — bit-accurate, fully deterministic |
| Advantage vs. analog CiM | Higher reliability — no analog noise at decision boundary |
| Area | Reduced vs. FF-based BNN (~60% less per cell) |

**Why this beats analog Compute-in-Memory (CiM):**

Analog CiM stores weights as analog voltages or currents and performs multiply-accumulate in the analog domain. This can achieve high efficiency but suffers from analog noise, drift, and device mismatch — especially at low $V_{DD}$. LAXOR is fully digital: the 1-bit decision is sharp, deterministic, and immune to the analog noise that plagues CiM at sub-threshold.

**Killer Fact:** LAXOR's 2315 TOPS/W is achieved not by a breakthrough device technology, but by eliminating the flip-flop — the most energy-wasteful circuit element in a sub-threshold digital design — through a careful co-design of storage and computation.

---

---

### Slide 17: IBM 5nm — The Production Frontier `[29:30–31:30]`

**Visual:** Two panels:
- Left: Cross-section comparison — FinFET (gate wraps 3 sides) vs. Nanosheet GAAFET (gate wraps all 4 sides). Annotate better electrostatic control and lower $V_{th}$ variation in GAAFET.
- Right: Dual-loop regulation block diagram — compiler feed-forward path (instruction decoder → frequency throttle) and hardware feedback path (on-die current sensors → PMIC). Show the 75W PCIe power cap as a hard ceiling.

**Content (IBM Research, ISSCC 2024 — 5nm GAAFET):**

LAXOR showed what's possible at 28nm with purpose-built sub-threshold logic. IBM's 2024 paper shows what happens when NTC becomes mandatory at the frontier — and the engineering required to make it work in a production AI SoC.

**Why 5nm forces NTC:**

At 5nm density, the thermal power density of a chip running all blocks at 1.1V nominal would exceed the heat dissipation capacity of any practical package. This is the Dennard wall that Eyal described — finally hitting AI SoCs at the frontier. NTC is not a power-saving option here: it is the only way to stay within the thermal envelope.

**The 5nm GAAFET advantage for NTC:**

FinFETs control the channel from three sides (tri-gate). In a **Nanosheet GAAFET**, the gate wraps entirely around the nanosheet channel on all four sides — providing superior electrostatic control. The practical benefit for sub-threshold operation:
- Better suppression of short-channel effects at low $V_{DD}$
- Reduced $V_{th}$ variation (better uniformity → less RDF impact)
- More predictable sub-threshold slope — easier to design the MEP analytically

**Multi-Voltage Domain Strategy:**

The SoC is partitioned into voltage domains based on timing criticality:
- Compute-heavy blocks (matrix multiply, attention): **0.55V** (NTC)
- Timing-critical paths (clock generation, control logic): **0.75V** (near-nominal)

This allows the power-hungry bulk of AI computation to run at NTC efficiency while a small fraction of the die handles precise timing at a higher voltage.

**The Dual-Loop Peak Current Regulation:**

The hardest problem at NTC in a 5nm SoC is not steady-state power — it is **peak current transients**. When a large matrix multiply begins, the current draw can spike by 5–10× in a single clock cycle. This causes a voltage droop on the Power Delivery Network (PDN) — the on-chip inductance and resistance convert the current surge into a $V_{DD}$ dip. At NTC, a 100mV droop can push the supply below the MEP and into the failure zone.

IBM's solution is a **two-tier control loop**:

1. **Software Feed-Forward (compiler-assisted):**  
   The compiler analyzes the instruction stream ahead of time, identifies high-current operations (GEMMs, attention heads), and inserts throttle commands into the instruction queue. The frequency controller receives these commands *before* the burst begins and proactively lowers the clock rate — preventing the peak from forming at all. Latency: ~10–100 clock cycles of look-ahead.

2. **Hardware Feedback (microsecond-scale):**  
   On-die current sensors continuously monitor $V_{DD}$ and supply current. If the feed-forward prediction misses (e.g., data-dependent branches), the hardware loop caps the current within microseconds — well within the PDN's response time. This enforces the **75W PCIe power limit** as a hard ceiling.

**Result:** **41% improvement** in BERT-Large inference throughput compared to a fixed-voltage NTC baseline without dual-loop regulation — achieved without exceeding thermal or power delivery budgets.

**Killer Fact:** IBM's 5nm SoC doesn't use NTC to save battery life. It uses NTC because the chip would physically overheat at full voltage. NTC has graduated from a research curiosity to a mandatory thermal management strategy at the production frontier.

---

---

### Slide 18: The Research Roadmap & Phase 04 `[31:30–33:00]`

**Visual:** Horizontal timeline arrow:
**Calhoun 2005** → **Dreslinski 2010** → **Zangi 2018** → **LAXOR 2023** → **IBM 2024** → **[ Phase 04: Our Proposal ]**

Below each paper, a one-line annotation of what problem it solved. Below Phase 04, the three open gaps.

**Content:**

The five papers we have covered today are not isolated results — they form a coherent research progression. Each paper solved the exact problem the previous one exposed.

| Paper | Problem Solved | Problem Left Open |
|-------|---------------|-------------------|
| Calhoun 2005 | Defined the MEP analytically | MEP shifts — no static margin is safe |
| Dreslinski 2010 | Quantified NTC as the practical operating point | Variability destroys the benefit |
| Zangi 2018 | ADVC tracks the MEP dynamically in real silicon | ADVC not validated at FinFET/GAAFET nodes; no SRAM loop |
| LAXOR 2023 | Brought sub-threshold to Edge AI inference | Only covers logic — no system-level MEP control |
| IBM 2024 | Deployed NTC in production 5nm AI SoC | No Zangi-style ADVC at 5nm; SRAM margin still static |

**The Three Open Gaps → Our Phase 04 Proposal:**

1. **GAAFET MEP Tracking:**  
   Calhoun's $W_{min}$ rule and Zangi's FFail architecture were derived on planar/FinFET nodes. Width quantization in 5nm GAAFET may invalidate the $W_{min}$ optimum, and the FFail delay line has not been validated in a GAAFET process. Phase 04 proposes adapting ADVC to the GAAFET operating model.

2. **Memory-Independent ADVC:**  
   Zangi's FFail covers logic paths only — SRAM read-disturb margin degrades independently (Eli's Slide 12) and is currently handled by a separate, static voltage floor. A dedicated read-disturb FFail monitor for the SRAM macros, integrated into the ADVC control loop, does not exist in the published literature.

3. **AI-Aware Fault Tolerance:**  
   BNN inference under NTC stress experiences soft errors — incorrect bit decisions at the decision boundary — before hard timing failures appear. A framework that allows the system to tolerate a controlled rate of soft BNN errors rather than forcing $V_{DD}$ upward on every near-miss could push the effective operating point below the current ADVC floor.

**Closing speaker note:** "Calhoun defined the target. Dreslinski told us where to stand. Zangi built the system to hold the position. LAXOR brought it to AI hardware. IBM brought it to production silicon. The question for Phase 04 is: what does the next paper in this sequence need to solve — and can we be the ones to frame it?"

---

**End of presentation.**

# Speaker 3 (Eli): Variability & ADVC
## Slides 11–14 | 18:00–26:00 (8 min)

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 11 | RDF & The Exponential Amplifier | 18:00–20:30 |
| Slide 12 | SRAM — The Weakest Link | 20:30–22:30 |
| Slide 13 | ADVC Architecture (Zangi et al., 2018) | 22:30–24:30 |
| Slide 14 | ADVC Silicon Results & The Guard-Band Payoff | 24:30–26:00 |

---

---

### Slide 11: RDF & The Exponential Amplifier `[18:00–20:30]`

**Visual:** Two panels:
- Left: cross-section of a sub-threshold transistor channel showing discrete dopant atoms randomly placed — a few atoms shifted = $V_{th}$ shift. Label "Random Dopant Fluctuation (RDF)" and a $\Delta V_{th} = 10\text{ mV}$ annotation.
- Right: Gaussian distribution of $V_{th}$ across a wafer. Mark the "Typical," "Fast" (left tail), and "Slow" (right tail) corners. Below it, show the corresponding distribution of path delay — much wider, with a 2–3× spread annotation.

**Content:**

Omri showed that the MEP shifts with temperature and process corner. This slide explains the microscopic mechanism that makes variability so catastrophic at sub-threshold — and why it is fundamentally worse here than in any other operating regime.

**Random Dopant Fluctuation (RDF):**

In a transistor channel, the dopants that set $V_{th}$ are discrete atoms, not a continuous sheet of charge. At advanced nodes, a typical transistor channel may contain only **50–200 dopant atoms**. Random statistical fluctuation in exactly where those atoms sit — varying by just one or two atoms — shifts $V_{th}$ by **10–30mV** from one transistor to the next on the same die.

In FinFETs, an additional mechanism adds to this: **Fin Line Edge Roughness (LER)** — the fin width varies by ±1–2nm due to lithographic imperfections, which directly modulates $V_{th}$.

**Why sub-threshold turns this into a disaster:**

Recall from Eyal's Slide 5 the sub-threshold current equation:
$$I_{sub} \propto e^{\frac{V_{GS} - V_{th}}{n V_T}}$$

A shift of $\Delta V_{th} = 10\text{ mV}$ in $V_{th}$ changes $I_{sub}$ by:
$$\frac{\Delta I}{I} = e^{\Delta V_{th}/nV_T} = e^{10\text{ mV}/36\text{ mV}} \approx 1.3\times$$

A **3σ event** — only three standard deviations from mean, happening routinely across millions of transistors on a die — gives $\Delta V_{th} \approx 90\text{ mV}$:
$$e^{90\text{ mV}/36\text{ mV}} \approx 12\times \text{ current variation}$$

Since $T_{clk} \propto 1/I_{sub}$, a 12× current variation becomes a **12× delay variation**. Across a critical path with 20 gates, timing spreads can reach **2–3× between the fast and slow ends of the wafer**.

**The worst-case margin trap:**

The only static solution to this is to design for the slowest possible device — the Slow corner, high temperature, end-of-life. This means setting $V_{DD}$ high enough that even the slowest transistors meet timing. In practice this pushes $V_{DD}$ **100–200mV above the typical-corner MEP**, erasing the entire energy benefit of sub-threshold operation.

**Killer Fact:** In a conventional 1V design, a 10mV $V_{th}$ shift changes $I_{on}$ by ~1% (linear regime). In sub-threshold, the same 10mV shift changes $I_{sub}$ by ~30%. The exponential law turns fabrication noise into a timing catastrophe.

---

---

### Slide 12: SRAM — The Weakest Link `[20:30–22:30]`

**Visual:** Schematic of a standard 6T SRAM cell — two cross-coupled inverters (M1–M4) and two access transistors (M5, M6). Draw two annotated failure scenarios:
- "Read Disturb" — arrow showing bitline BL rising and threatening to flip the storage node Q.
- "Write Margin collapse" — arrow showing WL asserted but M5/M6 too weak to overwrite the stored value at low $V_{DD}$.

**Content:**

Logic paths fail at sub-threshold when delay margin runs out. SRAM fails differently — and earlier.

**The 6T SRAM cell relies on a fight between transistors:**

A 6T cell stores state in two cross-coupled inverters. During a read, both access transistors (M5, M6) are turned on by the wordline (WL), connecting the storage nodes to the bitlines. This creates a voltage divider between the storage node and the bitline — and at low $V_{DD}$, this divider can pull the storage node far enough to flip the state. This is **Read Disturb**.

The **Static Noise Margin (SNM)** quantifies the cell's immunity to this. In strong inversion at 1.1V, SNM is large and comfortable. In sub-threshold:
- The access transistors (M5, M6) and the pull-down transistors (M1, M3) are both exponentially weak.
- The ratio of their drive currents — which determines SNM — becomes extremely sensitive to $V_{th}$ mismatch.
- By ~400mV, read SNM approaches zero for typical devices. At the Slow corner it reaches zero even earlier.

The **Write Margin** fails by the inverse mechanism: to write, you must overpower the cross-coupled inverters using M5/M6. At low $V_{DD}$, M5/M6 cannot source enough current to flip the cell reliably. Write failures begin appearing around $V_{DD} \approx 400\text{ mV}$ in standard 6T cells.

**The critical implication:**

In a real sub-threshold system, the question is never just "does the logic meet timing?" The SRAM imposes a **hard floor on $V_{DD}$** that is often 50–100mV *above* the logic MEP. In other words: the optimal $V_{DD}$ for logic is not achievable in practice because the memory fails first.

This is one of the three open gaps in the current literature — there is no dedicated ADVC loop for SRAM that tracks read-disturb margin independently of the logic FFail path. Our Phase 04 proposal targets exactly this.

**Q&A Prompt:** *Can you use a different SRAM cell topology to fix this?*  
→ Yes — 8T and 10T cells separate the read and write paths, improving SNM. But they cost 33–67% more area, and in minimum-sized sub-threshold design, that area penalty is significant.

---

---

### Slide 13: ADVC Architecture `[22:30–24:30]`

**Visual:** Full block diagram of the ADVC system from Zangi et al. (2018). Include:
- On-die sensors block: temperature sensor, $V_{th}$ monitor, aging monitor
- FFail delay line: a chain of inverters/buffers matching the critical path, with a "fail output" wire
- Comparator: fires when FFail path violates timing
- Digital control logic: up/down counter
- PMIC (Power Management IC): receives step-up/step-down commands
- Closed-loop arrow from PMIC back to the circuit supply rail
- Label the feedback loop "MEP Tracker"

**Content (Zangi et al., 2018 — 40nm TSMC):**

Omri showed the MEP shifts. Slide 11 showed why variability is destructive at the exponential operating point. Slide 12 showed SRAM fails before logic. The solution Zangi's team built is a closed-loop system that eliminates the static guard-band entirely by tracking the MEP in real time.

**The core design philosophy:**

Instead of taping out for the worst-case PVT corner and running at a fixed $V_{DD}$ that covers every scenario, ADVC designs for the *typical* corner and adapts dynamically when conditions change. The static guard-band — which was costing 100–200mV of energy overhead — is replaced by a real-time control loop.

**The three components:**

**1. On-Die Sensors:**
Four independent monitors on the same die as the logic:
- Temperature sensor (ring oscillator, sensitive to thermal variation)
- Process corner monitor ($V_{th}$ relative to a reference, captures die-to-die variation)
- Aging monitor (tracks BTI-induced $V_{th}$ shift over lifetime)
- Supply voltage monitor (closes the loop against PDN droop)

These sensors give the control system a continuous, real-time picture of the chip's operating condition — something a fixed tape-out margin can never provide.

**2. The First-Fail (FFail) Monitor:**

This is the key innovation. FFail is a **programmable replica critical path** — a delay chain built from the same standard cells as the real logic, tuned to have the same delay characteristics as the most critical path in the design. It is intentionally biased to fail *just before* the real logic fails — acting as a hardware tripwire.

When the chip's operating conditions (temperature, $V_{th}$, aging) degrade enough that the FFail path violates its timing window, an error signal fires. At this moment, the real logic is still meeting timing — but only barely. The control system gets the warning with enough margin to act.

**3. Closed-Loop Voltage Control:**

When FFail fires → the digital controller sends a step-up command to the PMIC, which increments $V_{DD}$ by **~25mV**. FFail stops firing → $V_{DD}$ is gradually walked back down in small steps until FFail fires again, then stepped up, and so on. The system continuously hunts the minimum $V_{DD}$ that keeps FFail silent.

This is cruise control for energy: always applying just enough voltage to be safe, never more.

---

---

### Slide 14: ADVC Silicon Results & The Guard-Band Payoff `[24:30–26:00]`

**Visual:** Two comparison bars:
- Left: ARM Cortex-M4F at nominal voltage — power consumption, frequency
- Right: Zangi ADVC SoC at 0.45V — same metrics
- Add a second diagram: $V_{DD}$ vs. time waveform showing the ADVC system hunting — stepping up when FFail fires, walking down slowly, steady-state oscillation around the MEP

**Content (Zangi et al., 2018 — 40nm TSMC):**

The ADVC SoC was fabricated in 40nm TSMC and tested across all PVT corners. Results:

| Metric | Value |
|--------|-------|
| Operating $V_{DD}$ range | **0.45V – 1.1V** (continuous, adaptive) |
| Active power at 0.45V | **18 µA/MHz** (≈ 8.1 µW/MHz at 0.45V) |
| Efficiency vs. Cortex-M4F | **3–5× better** at equivalent workload |
| PVT coverage | All corners — no functional failures |
| Guard-band eliminated | ~150mV static margin replaced by real-time loop |

**The guard-band payoff:**

A fixed-voltage chip targeting the worst-case Slow/Hot/Aged corner would run at ~600mV to guarantee functionality. The ADVC chip runs at 450mV on the same workload at the Typical corner — a **150mV reduction** that translates directly into energy savings via the $V_{DD}^2$ dynamic term and the reduced $T_{clk} \cdot I_{leak}$ leakage term.

**The limitation that sets up Phase 04:**

Zangi's FFail is a single unified monitor covering the logic critical paths. It has no visibility into SRAM read-disturb margin (Slide 12). In a real SoC, this means the memory must be separately over-provisioned with a static margin — partially defeating the purpose. A memory-aware ADVC loop, with a dedicated read-disturb FFail monitor for the SRAM macros, does not yet exist in the literature.

**Killer Fact:** The ADVC chip eliminates ~150mV of static guard-band margin. Since $E_{dyn} \propto V_{DD}^2$, operating at 450mV instead of 600mV saves $(600/450)^2 - 1 \approx 78\%$ of dynamic energy on the guarded portion alone.

---

**Handoff note:** Hand over to Nadav — the physics is established, the variability problem is solved at 40nm. Nadav shows what happens when you take these ideas to 28nm AI hardware and 5nm production silicon.

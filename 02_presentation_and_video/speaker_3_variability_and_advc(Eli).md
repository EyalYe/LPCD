# Speaker 3: Variability & ADVC
## Slides 6–7 | 18:00–26:00 (8 min) | Kahoot Q4 & Q5

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 6 | The Variability Nightmare | 18:00–23:00 |
| Slide 7 | ADVC — The Silicon Solution | 23:00–26:00 |
| Kahoot Q4 | FFail monitor role | after section |
| Kahoot Q5 | Which component fails first in sub-VT | after section |

---

---

### Slide 6: The Variability Nightmare `[18:00–23:00]`

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

### Slide 7: ADVC — The Silicon Solution `[23:00–26:00]`

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

## Kahoot — Speaker 3 Hosts `[after section, ~4 min]`

**Setup:** Launch Kahoot, share pin for Q4 and Q5. 90 seconds each (includes answer reveal and 20-second discussion).

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

**Handoff note:** After Kahoot Q5, hand over to Speaker 4 who shows how industry is applying these principles at the 28nm and 5nm frontier.

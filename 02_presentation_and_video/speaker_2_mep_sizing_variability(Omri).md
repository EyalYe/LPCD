# Speaker 2 — MEP Theory, Sizing & Variability (Omri)

**Topic:** The Minimum Energy Point — *Where exactly is the energy-optimal voltage, can we push it lower, and why does memory get in the way?*
**Time:** 10:00 – 20:00 (10 min slides, ~13 min with Q&A)
**Papers owned:** [1] Calhoun & Chandrakasan, *Sub-threshold design / Minimum Energy Operation* (the MEP & sizing paper) · NTC multi-core + SRAM voltage-domain material as the bridge to Eli & Nadav.
**Goal:** Turn Eyal's qualitative energy–delay trade-off into the *quantified* Minimum Energy Point (MEP); test whether transistor **sizing** can lower it; then show why **SRAM** breaks the single-voltage assumption and motivates separate voltage domains.

> **Scope note:** Eyal *motivated* the trade-off and *named* the cons. I make the energy story precise — the dynamic/leakage split, the U-shaped curve, and the sizing experiment — and I hand the *tracking* of this optimum in real silicon to Eli (ADVC) and the *robust memory/edge-AI* application to Nadav.

---

## Slide 1 — Recap & What This Section Adds (10:00 – 10:40)

**On screen**
- One-line callback to Eyal: lowering V_DD trades energy for performance, and the curve has a minimum.
- This section's two questions:
  1. **Where** is the Minimum Energy Point (MEP), and what sets it?
  2. **Can we do better** — can bigger transistors push the MEP lower?

**Speaker notes**
- Bridge from Eyal: he showed the *shape* of the energy–delay curve; I'll define the energy *minimum* on it precisely and then stress-test whether we can move it.
- Promise the payoff: a clean energy model, the U-curve, a sizing experiment that has a surprising result, and the memory problem that forces multiple supply rails.

---

## Slide 2 — Where Sub-Threshold Pays Off (10:40 – 11:30)

**On screen**
- Sub-threshold operation wins where **energy ≫ performance** in priority:
  - Medical implants · IoT devices · Wireless sensors · Wearable electronics
- These systems don't need high throughput — they need **years** on a small battery or harvested energy.
- Design goal: **minimize energy per operation** while still meeting a (modest) performance target.

**Speaker notes**
- Keep this brisk — it's the "why we care about the minimum at all" slide. For a CPU you optimize speed; for these devices you optimize joules-per-operation, full stop.
- Set up the next slide: if energy-per-op is the objective, we need an energy *model* to minimize.

---

## Slide 3 — The Energy Model: Dynamic vs. Leakage (11:30 – 13:00)

**On screen**
- Total energy per operation splits in two:
  **E_T = E_DYN + E_LEAK**
- **Dynamic energy:** **E_DYN = C_eff · V_DD²** → falls *quadratically* as V_DD drops.
- **Leakage energy:** in sub-threshold, **V_GS ≈ V_DD**. As V_DD drops the circuit gets dramatically slower, so delay **t_d** rises fast. Since leakage flows the whole cycle:
  **E_LEAK ∝ t_d** → *rises* as V_DD drops.
- The two terms pull in opposite directions → a built-in trade-off.

**Speaker notes**
- This is the heart of the math. Two competing terms: dynamic energy *wants* low voltage; leakage energy *punishes* low voltage because everything slows down and leakage integrates over a longer cycle.
- Emphasize the asymmetry: dynamic is a clean V_DD² law; leakage blows up because delay in sub-threshold grows *exponentially* as V_DD approaches and crosses V_th. That exponential is the same one Eyal flagged.

---

## Slide 4 — The Minimum Energy Point (13:00 – 14:30)

**On screen**
- *The key graph of the paper.* X-axis: supply voltage V_DD. Y-axis: energy per operation.
- As V_DD goes down: dynamic energy ↓, leakage energy ↑.
- Their sum forms a **U-shaped curve**; the bottom is the **Minimum Energy Point (MEP)** — the V_DD that gives the lowest energy per operation.
- The MEP typically sits in the **sub-threshold / near-threshold** region — *not* at zero volts.

**Speaker notes**
- Land the U-curve visually: left side dominated by leakage (too slow), right side dominated by dynamic energy (too high a voltage), minimum in between.
- This is the precise answer to Eyal's "there's an optimum" hand-wave. Below the MEP, dropping voltage *costs* you energy — exactly the "energy floor" con he named.

---

## Slide 5 — Can Sizing Improve the MEP? (14:30 – 15:50)

**On screen**
- The authors' question: **can larger transistors reduce energy?**
- The appeal: bigger devices → more drive current → the circuit still works correctly at a *lower* V_DD.
  - Measured: upsizing lowered the minimum functional V_DD by **~70 mV** for the tested technology.
- The catch: larger transistors also raise effective capacitance **C_eff**, and since **E_DYN = C_eff · V_DD²**, more capacitance pushes dynamic energy back **up**.

**Speaker notes**
- Frame it as a tempting-but-flawed idea. Upsizing genuinely buys you a lower functional voltage (~70 mV) — that part works.
- But it's not free: you pay in C_eff, and dynamic energy is *linear* in C_eff. So the question becomes whether the voltage win beats the capacitance penalty. Next slide answers it.

---

## Slide 6 — How Was It Tested, and the Surprise (15:50 – 17:00)

**On screen**
- Three-stage experiment:
  1. **Ring oscillator** → find the lowest V_DD where the circuit still works correctly.
  2. **Sub-threshold-optimized standard-cell libraries** → built for low-V operation.
  3. **FIR filter** built from those cells → measure real energy per operation.
- The surprising result:
  - Lower V_DD reduces dynamic energy, **but** the increased C_eff from upsizing raised E_DYN.
  - Lower V_DD also increased delay → raised E_LEAK.
  - Net: **E_T = E_DYN + E_LEAK actually *increased*.** Upsizing did **not** improve the MEP.

**Speaker notes**
- This is the payoff slide — slow down. The intuitive move (bigger = better at low V) backfires once you measure total energy on a real circuit.
- Takeaway to state out loud: for minimum-energy operation, **minimum-sized devices are generally optimal** — a result Nadav echoes later when LAXOR uses small, fused cells. Sizing buys robustness/voltage headroom, not energy.

---

## Slide 7 — NTC's Performance Problem & Parallelism (17:00 – 18:00)

**On screen**
- The second paper focuses on **Near-Threshold Computing (NTC)**.
- NTC saves a lot of energy — but a processor can become **~10× slower**.
- Proposed fix: **many low-voltage cores in parallel** instead of one fast core → recover throughput while keeping energy low.

**Speaker notes**
- Bridge from "what voltage" to "what architecture." Once you accept the ~10× slowdown, the way back to performance is width, not frequency — lots of slow, efficient cores.
- This sets up the *next* problem: many cores all lean on memory, and memory does not behave at these voltages.

---

## Slide 8 — SRAM Becomes the Bottleneck (18:00 – 19:00)

**On screen**
- Every core depends on **SRAM**.
- Problem: logic and SRAM do **not** share the same optimal voltage — generally **MEP_core ≠ MEP_sram**.
- SRAM typically needs a **higher** voltage for reliable read/write.
- A single global supply can't optimize both → you're forced to compromise one of them.

**Speaker notes**
- Connect back to Eyal's "SRAM stops working" con: here's the quantified consequence — the 6T cell's reliable operating voltage is above the logic's energy-optimal voltage.
- Pose the design tension cleanly: one rail can't serve two different optima. That's the setup for the fix.

---

## Slide 9 — Proposed Solution: Separate Voltage Domains (19:00 – 20:00)

**On screen**
- Use **separate voltage domains**:
  - Cores run near their own MEP (low V).
  - SRAM runs at a slightly higher, reliable V.
  - **Level converters** bridge the two domains.
- Benefits:
  - Better energy efficiency in the cores.
  - Better reliability in SRAM.
  - Independent optimization of logic and memory.

**Speaker notes**
- Close the loop: dual-rail design lets each block sit at its own optimum instead of a shared compromise — at the cost of level shifters and a more complex power grid.
- Handoff to Eli: "We've located the optimum and seen that even memory wants its *own* optimum. But the optimum isn't a fixed number — it drifts with process, temperature, and aging. Eli will show how a real chip *senses and tracks* it live with Adaptive Dynamic Voltage Control."

---

## Kahoot Questions (Speaker 2)

**Q1.** The total energy per operation in sub-threshold is best described as…
- A) Only dynamic energy, E = C·V_DD²
- ✅ **B) Dynamic + leakage: E_T = E_DYN + E_LEAK, which pull in opposite directions as V_DD drops**
- C) Only leakage energy
- D) Independent of supply voltage

**Q2.** Why does the total-energy curve form a **U-shape** with a Minimum Energy Point?
- A) Both dynamic and leakage energy fall as V_DD drops
- ✅ **B) Dynamic energy falls as V_DD drops, but leakage energy rises because the circuit slows down and leaks for longer**
- C) Energy is constant until the transistor turns off
- D) SRAM sets the minimum

**Q3.** The paper tested whether **upsizing transistors** lowers the MEP. What happened?
- A) Energy dropped dramatically — bigger is always better
- ✅ **B) It lowered the minimum functional V_DD (~70 mV) but raised C_eff and delay, so total energy actually increased**
- C) The circuit stopped working entirely
- D) It had no measurable effect

**Q4.** Why can't a single supply voltage serve both the logic cores and the SRAM at the MEP?
- A) SRAM uses no power
- ✅ **B) MEP_core ≠ MEP_sram — SRAM needs a higher voltage for reliable read/write than the logic's energy-optimal voltage**
- C) Logic always needs a higher voltage than SRAM
- D) They always share the exact same optimum

**Q5.** How does NTC recover the ~10× performance lost to low-voltage operation?
- A) By raising the clock frequency of a single core
- ✅ **B) Many low-voltage cores running in parallel instead of one fast core**
- C) By switching SRAM off
- D) By increasing the supply voltage back to nominal

---

## Visuals to prepare
- **The U-curve:** E_DYN (falling), E_LEAK (rising), and E_T = sum with the MEP marked at the bottom. *(Anchor slide — slide 4.)*
- **Sizing trade-off:** arrows showing "bigger device → lower functional V_DD" vs. "bigger device → higher C_eff → higher E_DYN." *(Slide 5.)*
- **Three-stage test flow:** Ring oscillator → sub-VT standard-cell library → FIR filter energy measurement. *(Slide 6.)*
- **Dual-voltage-domain block diagram:** core domain (low V) + SRAM domain (higher V) + level converters between them. *(Slide 9.)*

> **Coordination:** I own the MEP math, the sizing experiment, and the SRAM-voltage motivation. I do **not** cover the on-chip sensing/tracking hardware (Eli's ADVC) or the bit-accurate edge-AI accelerator (Nadav's LAXOR). I quantify the variability *consequence*; tracking it live is Eli's slide.

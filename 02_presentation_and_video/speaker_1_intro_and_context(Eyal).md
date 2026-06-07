# Speaker 1 — Introduction & Context (Eyal)

**Topic:** Introduction & Context — *Why operate circuits at extreme low voltages, and why it's hard.*
**Time:** 0:00 – 10:00 (10 min slides, ~13 min with Q&A)
**Papers owned:** [2] Dreslinski et al., *Near-Threshold Computing* (Proc. IEEE 2010) · [5] Schlachter, Camus & Enz, *Near/Sub-Threshold + Approximate Computing* (ISVLSI 2015)
**Goal:** Set the problem, define the operating regions, frame the energy–delay trade-off, **explain the real costs/cons of going low**, and introduce the two innovation axes the rest of the talk explores.

> **Scope note:** The Minimum Energy Point (MEP) — its math, sizing, and the *quantified* variability analysis — belongs to **Omri (Speaker 2)**. This section *motivates* the trade-off and *names* the problems as context; it does **not** define or solve the MEP.

---

## Slide 1 — Title & The Big Question (0:00 – 1:00)

**On screen**
- Project title: *Operating Circuits at Extreme Low Voltages for Maximum Energy Efficiency*
- Team: Eyal Yemini · Omri Adlan · Eli Becker · Nadav Dvora
- Hook line: **"What if the most efficient way to compute is to run the transistor while it's technically *off*?"**

**Speaker notes**
- Welcome the class. Frame the talk in one sentence: energy per operation — not raw speed — is the currency for a huge and growing class of devices.
- Motivate with the everyday: pacemakers, hearing aids, IoT sensors, wearables, and now edge-AI chips that must live for years on a coin cell or harvested microwatt.
- Promise the arc, and be honest up front: "Low voltage is a fantastic energy win — but it comes with serious costs, and most of this talk is about *paying* those costs."

---

## Slide 2 — Why Energy, Not Speed (1:00 – 2:30)

**On screen**
- The power wall / "dark silicon": after ~65 nm, scaling stopped delivering free energy gains; power budgets now strand transistors that can't all switch at once.
- Dynamic energy of a switching gate: **E_dyn = α·C·V_DD²** → energy scales with the **square** of supply voltage.
- Headline trade (paper 2): scaling nominal **1.1 V → ~400–500 mV** buys ~**10× energy** for ~**10× delay**.

**Speaker notes**
- The most powerful knob we have is V_DD, because dynamic energy is *quadratic* in it. Halve the voltage → roughly quarter the switching energy.
- There is no free lunch, and the next slides are mostly about the bill. The art of the field is choosing *where* on the energy–delay curve to sit — Omri will pin that point down precisely.
- "Dark silicon": modern chips can't power all transistors at once, so work-per-joule is now a first-class design goal, not a niche concern.

---

## Slide 3 — The Three Operating Regions (2:30 – 4:00)

**On screen (a labeled V_DD axis)**

| Region | V_DD vs. V_th | Energy | Speed | Where it's used |
|---|---|---|---|---|
| **Super-threshold** | V_DD ≫ V_th (~1.0–1.1 V) | High | Fast | Conventional CPUs/GPUs |
| **Near-threshold (NTC)** | V_DD ≈ V_th (~400–500 mV) | ~10× lower | ~10× slower | The "sweet spot" |
| **Sub-threshold** | V_DD < V_th (~250–350 mV) | Lowest | 50–100× slower | Sensors, implants, watches |

- The sub-threshold device is **not "off"** — it conducts a small **diffusion current**:
  **I_sub = I₀ · e^((V_GS − V_th)/n·v_t) · (1 − e^(−V_DS/v_t))**

**Speaker notes**
- Mental model: in normal logic the transistor is a switch that's off below V_th. Sub-threshold computing *uses the leakage current itself* as the working current.
- That single exponential term is the whole story — it's why we can compute at 250 mV (the *gift*), and, as the next slide shows, it's the root of nearly every drawback (the *curse*).
- NTC is the pragmatic middle ground: near V_th you keep most of the energy win without the brutal speed cliff of deep sub-threshold.

---

## Slide 4 — The Catch: What Low Voltage Costs You (4:00 – 6:30)  ⟵ *the cons*

**On screen — five linked consequences of that one exponential**

**1. Severe speed loss.**
- ~10× slower at NTC; **50–100× slower** in deep sub-VT. Drive current is now a tiny diffusion current, so every gate is sluggish. Clocks drop from GHz to kHz–low-MHz.
- *Knock-on:* you often need **massive parallelism** (many slow cores) to recover throughput — that's a whole architectural burden.

**2. The "variability nightmare" (PVT).**
- Current depends *exponentially* on (V_GS − V_th). So a *linear* spread in V_th — from **random dopant fluctuation** and **line-edge roughness** — becomes an **exponential** spread in current and delay.
- Effect: device-to-device speed can vary by up to ~**5×** (global σ/µ), total timing uncertainty up to ~**20×**. A chip that passes at the *typical* corner can **fail outright at the slow corner**.
- *And the foundry models lie:* standard PDK/SPICE models are extracted and calibrated for super-threshold; **they are inaccurate in the sub-VT region**, so you can't even fully trust simulation before silicon.

**3. Collapsing noise margins.**
- As V_DD shrinks toward the thermal voltage **v_t ≈ 26 mV** (room temp), the gap between logic '1' and '0' becomes tiny. Static noise margins fall, so coupling, IR-drop, and leakage-through-"off"-devices can flip a bit.

**4. SRAM stops working.**
- The classic **6T SRAM** cell is ratioed and is the first thing to break: read-disturb and write-margin failures explode. Reported 65 nm bitcell failure rate rises ~5 orders of magnitude (10⁻⁷ → ~4%) at low V — unacceptable for a real memory array. (Fixes = 8T/10T cells, assist circuits → Nadav.)

**5. Leakage can dominate the energy too.**
- Because everything runs so slowly, leakage current flows for a long time each cycle. Pushed far enough, leakage energy *overtakes* the dynamic savings — there's a point past which going lower **costs** more energy. *(Exactly where that point is = the MEP = Omri's slide.)*

**Speaker notes**
- Spend time here — this is the heart of the "context." Drive the single root cause: **the exponential I–V relationship**. Every con on the slide traces back to it.
  - Speed: working current is exponentially small.
  - Variability: a small V_th shift is *amplified* exponentially into current/delay.
  - Margins: signal levels approach the thermal-voltage floor.
  - SRAM: ratioed cells lose their stability when both pull-up and pull-down are weak and mismatched.
  - Energy floor: slow operation lets leakage accumulate.
- Hammer the "models lie" point — it's underappreciated and it's *why* this field leans so hard on on-chip sensors and adaptive control (Eli's ADVC). You design with margin because you literally can't fully simulate the corner.
- Then map each con to the rest of the talk: variability → Omri quantifies it; tracking/adapting to it → Eli (ADVC); SRAM robustness → Nadav. This slide is the agenda.

---

## Slide 5 — The Energy–Delay Trade-off, Visually (6:30 – 7:30)
*(Paper 2 — Dreslinski et al.)*

**On screen**
- Lowering V_DD trades **energy for delay**, but *not linearly* across regions:
  - 1.1 V → NTC: ~**10× energy** for ~**10× delay** (great deal)
  - NTC → deep sub-VT: only ~2× *more* energy for another **50–100×** delay (bad deal)
- So there is an optimal voltage that minimizes energy/op — **defining and locating it is Omri's section.**

**Speaker notes**
- Takeaway is *shape*, not numbers: the energy curve flattens while the delay penalty explodes below near-threshold. That's why NTC is the sweet spot and why nobody just sets V_DD to zero.
- One clean forward-pointer: "There's an energy *minimum* on this curve — Omri will define it, compute it, and show how transistor sizing moves it."

---

## Slide 6 — A Second Axis: Approximate Computing (7:30 – 9:00)
*(Paper 5 — Schlachter, Camus & Enz, EPFL 2015)*

**On screen**
- Voltage scaling is **one** lever. For **error-tolerant** workloads (DSP, image/video, ML) there's a second: deliberately compute *approximately*.
- Two automatic techniques for arithmetic circuits:
  - **Gate-level pruning** — rank gates by *Significance × Activity Product (SAP)*, remove the least important; errors concentrate in the LSBs.
  - **Inexact Speculative Adders (ISA)** — split the carry chain into parallel speculate→add→compensate segments; shorter critical path.
- **Savings multiply** and **directly attack the cons**: relaxing exactness stacks on top of the V_DD² win, *and* shorter critical paths shorten delay and shrink the timing-variability window.
- 32-bit adders (65 nm): up to **85% PDAP reduction** at 1% error each; combined ("mixed") → **~20× EDAP saving** at 2% RMS error. Demonstrated on a 180 nm sub-threshold library.

**Speaker notes**
- Big idea: low-voltage and approximate computing are *complementary*. One attacks energy via physics (V_DD²); the other via the architecture (fewer, shorter switching paths) — and the latter also helps with two of our cons (delay and variability).
- Honest caveat: only valid where bounded output error is acceptable; it's a co-design problem — accuracy budget vs. energy vs. variability.
- Set up a later contrast: paper 5 trades accuracy for energy; LAXOR (Nadav, paper 4) stays *bit-accurate*. Two philosophies.

---

## Slide 7 — Roadmap & Handoff (9:00 – 10:00)

**On screen — the logical arc / timeline**
> **Theory of the optimum (1, 2005)** → **the NTC sweet spot (2, 2010)** → **a complementary efficiency axis (5, 2015)** → **production silicon vs. variability (3, 2018)** → **a modern edge-AI use case (4, 2023)**

- Who solves which con:
  - **Omri** — MEP theory & math, sizing, and the *quantified* variability problem (paper 1)
  - **Eli** — Adaptive Dynamic Voltage Control: sensing & tracking the optimum despite PVT and aging (paper 3)
  - **Nadav** — RDF , the LAXOR edge-AI accelerator & conclusions (paper 4)

**Speaker notes**
- Recap three takeaways: (1) energy is quadratic in V_DD; (2) the price is speed, variability, weak margins, fragile SRAM, and an energy floor — all rooted in the exponential I–V; (3) the rest of the talk is how engineers *beat* those cons.
- Handoff line: "I've shown you *why* we go low and *what it costs*. Omri will now make the energy story precise — the Minimum Energy Point, the sizing behind it, and the variability we just met, quantified."

---

## Kahoot Questions (Speaker 1)

**Q1.** Dynamic switching energy of a gate scales with the supply voltage as…
- A) Linearly (∝ V_DD)
- ✅ **B) Quadratically (∝ V_DD²)**
- C) Exponentially (∝ e^V_DD)
- D) It doesn't depend on V_DD

**Q2.** Why is *variability* so much worse in sub-threshold than in normal logic?
- A) Wires get longer at low voltage
- ✅ **B) Current depends *exponentially* on V_GS − V_th, so a small V_th spread becomes a huge current/delay spread**
- C) The clock frequency is too high
- D) There is no variability difference

**Q3.** Which is a real drawback of deep sub-threshold operation?
- A) Faster than super-threshold
- B) Larger noise margins
- ✅ **C) ~50–100× slower, fragile 6T SRAM, and foundry device models are inaccurate in this region**
- D) Zero leakage current

**Q4.** Compared to deep sub-threshold, Near-Threshold Computing (≈ V_th) is attractive because…
- ✅ **A) It keeps most of the ~10× energy savings while avoiding the 50–100× speed penalty**
- B) It is faster than super-threshold
- C) It completely eliminates PVT variability
- D) It uses no leakage current at all

**Q5.** Approximate computing pairs well with low voltage partly because…
- A) It raises the supply voltage
- ✅ **B) Its energy savings stack on the V_DD² win, and shorter critical paths reduce delay and timing-variability**
- C) It guarantees zero output error
- D) It only works in super-threshold circuits

---

## Visuals to prepare
- **V_DD axis** showing the three regions relative to V_th. *(Key intro slide.)*
- **"The Catch" con-map**: one exponential I–V at the center, five arrows out to Speed / Variability / Noise Margin / SRAM / Energy-floor. *(Anchor slide for the cons.)*
- **Energy–delay trade curve**: favorable 1.1 V → NTC step vs. steep NTC → sub-VT cliff — *without* labeling/solving the MEP (Omri's slide).
- **Before/after netlist** sketch for gate-level pruning (LSB errors) — optional, keep simple.

> **Coordination:** No MEP curve, no Lambert-W, no sizing math here — Speaker 2 owns all of it. The cons are *named and explained as context*; their *solutions* are explicitly handed to Omri, Eli, and Nadav.

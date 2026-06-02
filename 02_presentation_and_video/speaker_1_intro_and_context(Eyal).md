# Speaker 1: Introduction & Context
## Slides 1–3 | 0:00–10:00 (10 min) | Kahoot Q3

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 1 | Title Slide | 0:00–1:00 |
| Slide 2 | Why Are We Here? (Motivation) | 1:00–5:00 |
| Slide 3 | The Operating Regions | 5:00–10:00 |
| Kahoot Q3 | NTC 10×/10× rule | after section |

---

---

### Slide 1: Title Slide `[0:00–1:00]`

**Title:** Operating Circuits at Extreme Low Voltages  
**Subtitle:** Foundations, Adaptive Control, and Industry Applications  
**Presenters:** [Name 1] · [Name 2] · [Name 3] · [Name 4]

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

## Kahoot — Speaker 1 Hosts `[after section, ~2 min]`

**Setup:** Launch Kahoot, share pin for Q3. 90 seconds (includes answer reveal and 20-second discussion).

---

### Question 3
**"According to Dreslinski et al. (2010), operating at Near-Threshold (NTC) vs. nominal voltage gives approximately:"**
- A) 100× energy improvement, 2× performance loss
- B) 10× energy improvement, 10× performance loss ✓
- C) 2× energy improvement, 50× performance loss
- D) 5× energy improvement, no performance loss

*After reveal:* "The '10×/10×' rule. And going deeper to 250mV only adds another 2× energy — but 100× more slowdown. NTC is the practical sweet spot."

---

**Handoff note:** After Kahoot Q3, hand over to Speaker 2 who covers the MEP math behind *why* the NTC is optimal.

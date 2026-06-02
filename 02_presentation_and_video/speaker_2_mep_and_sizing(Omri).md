# Speaker 2: MEP Theory & Sizing
## Slides 4–5 | 10:00–18:00 (8 min) | Kahoot Q1 & Q2

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 4 | The Minimum Energy Point (MEP) | 10:00–15:00 |
| Slide 5 | Sizing for Energy — "Small is Beautiful" | 15:00–18:00 |
| Kahoot Q1 | MEP voltage range | after section |
| Kahoot Q2 | Sizing rules ($W_{min}$) | after section |

---

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
→ No. It shifts dynamically with temperature, process corner, and aging. This is why static design-time margins fail — and why we need ADVC (covered in Speaker 3's section).

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

---

## Kahoot — Speaker 2 Hosts `[after section, ~4 min]`

**Setup:** Launch Kahoot, share pin for Q1 and Q2. 90 seconds each (includes answer reveal and 20-second discussion).

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

**Handoff note:** After Kahoot Q2, hand over to Speaker 3 who covers why PVT variation makes hitting the MEP so difficult — and the ADVC solution.

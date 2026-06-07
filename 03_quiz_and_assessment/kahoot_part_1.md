# Kahoot — Part 1: Introduction & Context (Eyal)

**Source section:** [Speaker 1 — Introduction & Context](../02_presentation_and_video/speaker_1_intro_and_context(Eyal).md)
**Topics:** energy vs. speed, the three operating regions, the cons of low voltage, the energy–delay trade-off, approximate computing.
**Difficulty:** challenging — each item needs a calculation or multi-step reasoning, not recall.

---

**Q1.** A block runs at 1.0 V and is rescaled to 0.5 V. With activity factor α and capacitance C unchanged, and ignoring leakage, its **dynamic energy per operation** becomes approximately…
- A) Half (2× lower)
- ✅ **B) One quarter (4× lower) — because E_dyn = α·C·V_DD² and (0.5/1.0)² = 1/4**
- C) One eighth (8× lower)
- D) Unchanged — energy doesn't depend on V_DD

**Q2.** Sub-threshold drain current follows I_sub ∝ e^((V_GS − V_th)/(n·v_t)). A *linear* device-to-device spread in V_th produces a far larger spread in delay because…
- A) Delay is independent of drive current
- ✅ **B) Current depends *exponentially* on V_th, and the sensitivity d(ln I)/dV_th = −1/(n·v_t) grows as n·v_t shrinks**
- C) The thermal voltage v_t rises in proportion to V_th
- D) The spread only affects interconnect, not transistors

**Q3.** Pushing from near-threshold into deep sub-threshold buys only ~2× more energy savings but 50–100× more delay, and *past some point* lowering V_DD **raises** total energy. The dominant cause is…
- A) Dynamic energy α·C·V_DD² begins to increase as V_DD falls
- ✅ **C) The circuit gets so slow that leakage integrates over a much longer cycle, so leakage energy overtakes the still-shrinking dynamic energy**
- B) The threshold voltage V_th increases as V_DD decreases
- D) Wire capacitance suddenly dominates the switching energy

**Q4.** Static noise margins collapse near the thermal-voltage floor v_t = kT/q (~26 mV at 300 K). Which change makes the margin problem **worse**, all else equal?
- A) Cooling the die toward 0 K
- ✅ **B) Raising the operating temperature, since v_t = kT/q ∝ T, lifting the floor and squeezing the '1' vs. '0' separation**
- C) Raising V_DD far above V_th
- D) Reducing random dopant fluctuation

**Q5.** In automatic **gate-level pruning**, gates are ranked and removed by their *Significance × Activity Product (SAP)*. Removing the lowest-SAP gates first is attractive because those gates…
- A) Lie on the most-significant bits, so accuracy is preserved exactly
- ✅ **B) Contribute least to the output value *and* switch rarely, so the error concentrates in the LSBs while energy and critical-path length both drop**
- C) Lengthen the carry chain, which improves timing margin
- D) Guarantee the result stays bit-exact regardless of how many are cut

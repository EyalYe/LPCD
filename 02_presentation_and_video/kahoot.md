# Kahoot — All Questions
## 6 Questions | 33:00–45:00 (12 min total)
**Format:** 90 seconds per question (includes answer reveal + 20-second discussion)

---

## Question Order & Host Assignment

| # | Topic | Hosted By | Timing |
|---|-------|-----------|--------|
| Q1 | MEP voltage range (Calhoun 2005) | Omri | after Omri's section |
| Q2 | Sizing rules ($W_{min}$) (Calhoun 2005) | Omri | after Omri's section |
| Q3 | NTC 10×/10× rule (Dreslinski 2010) | Eyal | after Eyal's section |
| Q4 | FFail monitor role (Zangi 2018) | Eli | after Eli's section |
| Q5 | Which component fails first in sub-VT | Eli | after Eli's section |
| Q6 | IBM 5nm BERT-Large technique | Nadav | after Nadav's section |

---

---

### Question 1 — hosted by Omri
**"According to Calhoun et al. (2005), what is the typical voltage range of the Minimum Energy Point (MEP) in a standard CMOS process?"**
- A) 50–100 mV
- B) 250–400 mV ✓
- C) 600–800 mV
- D) 1.0–1.2 V

*After reveal:* "Below the MEP, you're actually wasting energy. The leakage term dominates because the circuit takes exponentially longer to finish."

---

### Question 2 — hosted by Omri
**"Calhoun's sizing rule for sub-threshold circuits is the opposite of standard design. What is the correct rule?"**
- A) Upsize everything for speed
- B) Size for the worst-case PVT corner
- C) Use minimum width and length ($W_{min}$, $L_{min}$) ✓
- D) Double the width to reduce threshold sensitivity

*After reveal:* "Upsizing in sub-VT raises both capacitance and leakage without a proportional speedup — it's always a losing trade."

---

### Question 3 — hosted by Eyal
**"According to Dreslinski et al. (2010), operating at Near-Threshold (NTC) vs. nominal voltage gives approximately:"**
- A) 100× energy improvement, 2× performance loss
- B) 10× energy improvement, 10× performance loss ✓
- C) 2× energy improvement, 50× performance loss
- D) 5× energy improvement, no performance loss

*After reveal:* "The '10×/10×' rule. And going deeper to 250mV only adds another 2× energy — but 100× more slowdown. NTC is the practical sweet spot."

---

### Question 4 — hosted by Eli
**"In Zangi's ADVC system, what is the role of the 'First-Fail (FFail)' monitor?"**
- A) It permanently disables failing logic paths
- B) It reports errors to the operating system for software correction
- C) It is a programmable delay path that trips just before real logic fails, providing an early warning to increase $V_{DD}$ ✓
- D) It measures SRAM read disturb margins

*After reveal:* "FFail is a hardware tripwire. It fires before any functional error — so the voltage is boosted proactively, not reactively after a fault."

---

### Question 5 — hosted by Eli
**"Which component of a standard digital design fails FIRST when $V_{DD}$ is reduced into the sub-threshold range?"**
- A) Combinational logic gates
- B) Clock distribution networks
- C) Standard 6T SRAM cells ✓
- D) Phase-locked loops (PLLs)

*After reveal:* "SRAM read SNM collapses before logic timing fails. This means memory stability — not logic delay — often sets the real lower bound on $V_{DD}$."

---

### Question 6 — hosted by Nadav
**"IBM's 2024 5nm AI SoC achieved a 41% improvement in BERT-Large throughput using which technique?"**
- A) Increasing the clock frequency beyond the datasheet limit
- B) Moving all logic to a deep sub-threshold supply (200mV)
- C) Software-assisted feed-forward prediction combined with hardware current feedback in the NTC regime ✓
- D) Replacing all SRAM with eDRAM macros

*After reveal:* "The compiler predicts current bursts before they happen and throttles proactively. This is the hardware-software co-design approach that makes NTC practical at the 5nm frontier."

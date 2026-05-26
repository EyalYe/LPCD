# Presentation Content: Extreme Low Voltage Computing

## Slide 1: Title Slide [Speaker: Member A]
**Title**: Operating Circuits at Extreme Low Voltages: Foundations, ADVC, and Future Trends
**Subtitle**: Maximizing Energy Efficiency in Nanoscale VLSI
**Lecturers**: [Group Names]

---

## Slide 2: Beyond Traditional Power Savings [Speaker: Member A]
**Visual**: A funnel showing traditional techniques (Clock Gating -> Power Gating -> DVFS) narrowing down to "Near/Sub-Threshold."
- **Current State**: We know how to save power by shutting things down or scaling voltage within standard limits.
- **The Efficiency Wall**: Traditional scaling is hitting a limit where static leakage dominates.
- **Our Focus**: Crossing the threshold boundary ($V_{th}$) to reclaim energy efficiency for the next generation of IoT and Edge AI.
**Speaker Notes**: "We've all studied clock gating and power gating. Those are essential, but they are reactive. Today, we are talking about a proactive architectural shift: operating where the transistor is barely 'on.' This is where we find the theoretical maximum energy efficiency."

---

## Slide 3: Motivation: Why Sub-threshold? Why Now? [Speaker: Member A]
**Visual**: Three pillars: (1) Smart Dust/IoT, (2) Biomedical Implants, (3) 14Å Thermal Wall.
- **The Power Crisis**: We have billions of sensors but limited energy. We can't change batteries in a smart-city sensor or a heart monitor.
- **Edge AI**: Running a neural network on a tiny budget requires us to drop $V_{DD}$ to the absolute minimum.
- **Nanoscale Reality**: At the 14Å process node, power density is so high that traditional scaling hits a "thermal wall." Sub-threshold isn't just an option; for some "Always-On" blocks, it's the only way to stay within the thermal envelope.
**Speaker Notes**: "Why are we even looking at sub-threshold? Because we've run out of room. Whether it's a biomedical implant that needs to last 10 years or a 3nm AI chip that would melt if every block ran at 1.1V, extreme low voltage is the only path forward for high-density, energy-constrained systems."


---

## Slide 4: Defining the Regions [Speaker: Member A]
**Visual**: $I_d$ vs $V_{gs}$ curve on a log scale, highlighting Sub-VT, Near-VT, and Super-VT regions.
- **Sub-threshold ($V_{DD} < V_{th}$)**:
    - Current is exponential, not linear/quadratic.
    - $I_{sub} \propto e^{(V_{GS}-V_{th})/nV_T}$.
    - Extreme efficiency, but very slow (kHz-MHz).
- **Near-threshold ($V_{DD} \approx V_{th}$)**:
    - The 'Optimal' region for many commercial applications.
    - 10x energy saving with manageable performance loss.
**Speaker Notes**: "In sub-threshold, we don't have a strong inversion layer. We rely on diffusion current—the same physics behind a diode. It’s exponential, which means every millivolt counts twice as much as it does in standard digital logic."

---

## Slide 5: Minimum Energy Point (MEP) Theory [Speaker: Member B]
**Visual**: The "U-shaped" energy curve showing $E_{dyn}$ (decreasing), $E_{leak}$ (increasing), and $E_{tot}$ with a clear minimum.
- **Equation**: $E_{tot} = \alpha C V_{DD}^2 + V_{DD} I_{leak} T_{clk}$.
- **The Trade-off**:
    - As $V_{DD} \downarrow$, $E_{dyn}$ drops quadratically.
    - However, $T_{clk} \uparrow$ exponentially in sub-threshold.
    - Eventually, the circuit takes so long to finish that the 'trickle' of leakage energy exceeds the dynamic savings.
**Speaker Notes**: "This is the most important slide for the exam. The Minimum Energy Point isn't at 0V. It's usually between 200mV and 400mV. If you go lower than the MEP, you are actually *wasting* energy because you're leaking for too long."

---

## Slide 6: Sizing for Energy (Calhoun et al., 2005) [Speaker: Member B]
**Visual**: Comparison of standard cell vs. MEP-optimized cell.
- **Speed vs. Energy**: In standard design, we size up to go faster.
- **Sub-VT Rule**: Minimum width ($W_{min}$) is often optimal.
- **Logic**: Increasing width increases both capacitance (more $E_{dyn}$) and leakage (more $E_{leak}$) without a significant enough speedup to offset both.
**Speaker Notes**: "A key takeaway from Calhoun's seminal paper: don't upsize. In sub-threshold, 'Small is Beautiful.' Minimum sized transistors give you the best energy-delay product because they minimize the static footprint."

---

## Slide 7: The Variability Nightmare [Speaker: Member B]
**Visual**: A distribution graph showing how $I_{on}/I_{off}$ ratios vary wildly across process corners (SS, TT, FF) in sub-VT.
- **The Problem**: In sub-threshold, $I \propto e^{V_{gs}-V_{th}}$. 
- **The Result**: A tiny $10mV$ shift in $V_{th}$ can cause a **2-3x change** in circuit delay.
- **The Risk**: Functional failure. Logic gates may not even toggle if the pull-up is too weak compared to pull-down leakage.
**Speaker Notes**: "Why isn't everything sub-threshold yet? Because of this. In the 3nm or 5nm nodes, process variation is huge. In standard logic, it's a nuisance. In sub-threshold, it's a catastrophe. You need something smarter than fixed-voltage design."

---

## Slide 8: Advanced Adaptive Dynamic Voltage Control (ADVC) [Speaker: Member B]
**Visual**: Block diagram of the system from Zangi et al. (2018).
- **On-Die Monitoring**: Continuous sensing of temperature, process corner, and aging.
- **First-Fail (FFail) Sensors**: Programmable paths that 'warn' the system before the actual logic fails.
- **The Closed Loop**: The SOC automatically bumps $V_{DD}$ just enough to stay functional at the MEP, even as the environment changes.
**Speaker Notes**: "This is the state-of-the-art solution from the Zangi paper. Instead of designing for the 'worst case' and wasting energy, we design for the 'typical case' and use ADVC to adjust in real-time. It's like having a cruise control that optimizes for fuel efficiency regardless of the terrain."

---

## Slide 9: Future Industry Use Cases [Speaker: Member C]
**Visual**: A collage of an AI chip, a medical implant, and a microscopic sensor.
- **Edge AI & BNNs**: Neural networks that can run on microwatts by using Latch-XOR logic instead of heavy registers.
- **Biomedical**: Neural stimulators and heart monitors that last for years on a single button cell.
- **Batteryless IoT**: Chips powered by ambient RF or thermal gradients.
**Speaker Notes**: "Where is this going? Everywhere that power is expensive or batteries are impossible. We are seeing sub-VT moving from academic curiosities into high-end 3nm AI chips and implantable medical tech."

---

## Slide 10: Conclusion: A Paradigm Shift [Speaker: Member C]
- **Summary**:
    - MEP is the goal.
    - Variation is the enemy.
    - ADVC is the tool.
- **Next Step**: In Step 04, we will propose a novel modification to the FFail mechanism to improve energy tracking in 14Å processes.
**Speaker Notes**: "To wrap up: operating at extreme low voltages is no longer a 'low speed' niche. It is the mandatory path for the future of energy-efficient nanoscale VLSI. Thank you."

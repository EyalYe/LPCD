# Speaker 1 (Eyal): Introduction & Context
## Slides 1–6 | 0:00–10:00 (10 min)

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 1 | Title | 0:00–1:00 |
| Slide 2 | The End of the Free Lunch (Dennard Scaling) | 1:00–3:00 |
| Slide 3 | Two Problems, One Solution | 3:00–5:00 |
| Slide 4 | MOSFET Physics Below $V_{th}$ | 5:00–7:00 |
| Slide 5 | The Three Operating Regimes | 7:00–8:30 |
| Slide 6 | The NTC Sweet Spot (Dreslinski et al., 2010) | 8:30–10:00 |

---

---

### Slide 1: Title Slide `[0:00–1:00]`

**Title:** Operating Circuits at Extreme Low Voltages  
**Subtitle:** Foundations, Adaptive Control, and Industry Applications  
**Presenters:** Eyal Yemini · Omri Adlan · Eli Becker · Nadav Dvora

**Speaker Note:** Brief welcome. "By the end of this lecture, you will understand why operating below the threshold voltage is not a compromise — it is the only way forward for the next generation of chips. We will cover the physics, the math, the silicon solution, and where industry is taking this today."

---

---

### Slide 2: The End of the Free Lunch `[1:00–3:00]`

**Visual:** Two-panel graph:
- Left: Classic Dennard Scaling chart — transistor count, frequency, and $V_{DD}$ vs. year. Frequency and $V_{DD}$ flatline after ~2005 while transistor count keeps rising.
- Right: Power density vs. node (W/cm²) — rising steeply from 65nm onward, with a "100 W/cm² = nuclear reactor core" annotation at the right edge.

**Content:**

For three decades the industry coasted on **Dennard Scaling** (1974): as transistors shrank, voltage and current scaled proportionally with geometry, so power density remained roughly constant. Smaller meant faster *and* cooler. Every new process node was a free performance win.

**Dennard Scaling broke down around 2005.** Three hard physical limits prevent $V_{DD}$ from scaling further:

1. **Leakage floor:** $V_{th}$ cannot be scaled below ~0.3–0.4V or the transistor never turns off — subthreshold leakage current becomes uncontrollable.
2. **Reliability:** Gate oxide breakdown and hot-carrier injection impose hard lower bounds on $V_{DD}$ at every node.
3. **Noise margins:** Logic cells need a minimum voltage swing to reliably distinguish a '0' from a '1'.

The consequence: $V_{DD}$ has been stuck at 0.8–1.1V since 2005 while transistor count per die has continued doubling every 2 years. Power density is rising steeply. At leading-edge nodes today, CPU power density exceeds **100 W/cm²** — comparable to the surface of a nuclear reactor core.

**Killer Fact:** Frequency scaling stopped at ~4 GHz around 2004 for exactly this reason. The industry pivoted to multi-core to keep delivering performance — but that only deferred the problem. Power density kept climbing.

---

---

### Slide 3: Two Problems, One Solution `[3:00–5:00]`

**Visual:** Two-column layout with icons:
- Left column: IoT sensor on a solar cell, brain implant schematic, bridge-embedded sensor — labelled "Constrained Systems"
- Right column: Dense 5nm AI SoC die photo with thermal map overlay — labelled "High-Density Systems"
- Below both columns: A single downward arrow pointing to "Proactive Energy Minimization"

**Content:**

The Dennard wall creates two distinct engineering challenges that both converge on the same solution.

**Constrained Systems — you have no power to waste:**
- A neural implant draws ~10 µW. It cannot be recharged. It must last 10 years.
- A smart city sensor is buried in a bridge pillar, harvesting microwatts from vibration.
- These systems cannot afford to run at 1V — they *must* operate at the physical minimum energy per operation.

**High-Density Systems — you can't shed the heat:**
- A 5nm AI SoC running all blocks at 1.1V would exceed the thermal dissipation capacity of the package.
- NTC is not a power-saving option for these chips — it is the only way to prevent thermal shutdown.
- IBM (2024) demonstrated this is now mandatory, not niche (Nadav covers this in detail).

**Reactive vs. Proactive Power Management:**

We have studied clock gating, power gating, and DVFS. These are *reactive* tools — you shed load when you have headroom. They assume you can afford to run at nominal voltage most of the time.

Today's approach is fundamentally different: **proactive energy minimization** — designing the circuit from the ground up to operate at the absolute physical minimum energy point, continuously.

**Q&A Prompt:** *Why not just use aggressive power gating instead?*  
→ Power gating turns the system completely off. Sub-threshold computing enables continuous always-on operation — which is a strict requirement for biosensors, security monitors, and always-listening interfaces.

---

---

### Slide 4: MOSFET Physics Below $V_{th}$ `[5:00–7:00]`

**Visual:** Band diagram of an NMOS transistor in two states side by side:
- Left: $V_{GS} > V_{th}$ — strong inversion layer at the Si-SiO₂ interface, channel full of electrons.
- Right: $V_{GS} < V_{th}$ — no inversion layer, but a small carrier population visible near the surface (weak inversion). Label the diffusion current arrow.

**Content:**

In standard digital design we treat the transistor as a switch: $V_{GS} > V_{th}$ = on, $V_{GS} < V_{th}$ = off. Sub-threshold computing is built on the fact that **"off" is not zero current**.

**What is $V_{th}$ physically?**  
$V_{th}$ is the gate voltage at which the surface potential at the Si-SiO₂ interface becomes large enough to invert the p-type silicon — creating a thin **strong inversion layer** where electrons flood in and conduct. Above $V_{th}$, current flows by **drift**: carriers are pulled by the electric field. This is the familiar quadratic $I_D \propto (V_{GS} - V_{th})^2$ regime.

**Below $V_{th}$ — weak inversion:**  
No strong inversion layer forms, but thermal excitation still creates a small minority carrier population near the surface. These carriers move by **diffusion** — driven by a concentration gradient across the channel, not by the electric field. This is physically identical to minority carrier injection in a BJT: current is governed by the Boltzmann factor $e^{qV/kT}$, not by a quadratic law.

**The thermal voltage:**
$$V_T = \frac{kT}{q} \approx 26 \text{ mV at 300 K}$$

This single number sets the energy scale for everything that follows. It gives rise to the **subthreshold swing** — the gate voltage required to increase drain current by one decade:
$$SS = n \cdot V_T \cdot \ln(10) \approx 60\text{–}70 \text{ mV/decade}$$

where $n$ is the body-effect ideality factor (1.2–1.8 in real devices), accounting for the capacitive divider between the gate oxide and the depletion region.

**60 mV/decade is a fundamental thermodynamic limit.** At room temperature, no conventional MOSFET can switch faster than one decade of current per 60 mV of gate voltage. This is why steep-slope devices — tunnel FETs, ferroelectric FETs — are active research areas: they try to break this wall.

**Killer Fact:** The 60 mV/decade limit is not a fabrication problem. It is set by $kT/q$ — the thermal energy of carriers at room temperature. The only way to beat it is to cool the chip or change the switching mechanism entirely.

---

---

### Slide 5: The Three Operating Regimes `[7:00–8:30]`

**Visual:** $I_D$ vs $V_{GS}$ on a **log scale**. Three clearly annotated regions: Sub-threshold, Near-threshold (NTC), Super-threshold. $V_{th}$ marked with a vertical dashed line. A second horizontal axis below shows energy efficiency (high ← → low) and performance (low ← → high) for each region.

**Content:**

| Regime | $V_{DD}$ | Current Mechanism | Speed | Energy vs. Nominal |
|--------|----------|------------------|-------|--------------------|
| Super-threshold | $V_{DD} \gg V_{th}$ | Drift (quadratic) | GHz | Baseline |
| Near-threshold (NTC) | $V_{DD} \approx V_{th}$ | Mixed | 10–100 MHz | ~10× better |
| Sub-threshold | $V_{DD} < V_{th}$ | Diffusion (exponential) | kHz–MHz | ~20× better |

**The sub-threshold current equation:**
$$I_{sub} = I_0 \cdot e^{\frac{V_{GS} - V_{th}}{n V_T}} \cdot \left(1 - e^{\frac{-V_{DS}}{V_T}}\right)$$

- The **first exponential term** is the Boltzmann injection over the source-channel barrier — the source of the efficiency gain.
- The **second term** saturates to 1 once $V_{DS} > 3V_T \approx 80$ mV, which is always true in practice.
- The exponential dependence on $V_{GS} - V_{th}$ is both the opportunity and the hazard: every millivolt of $V_{GS}$ has an amplified effect. Omri will show exactly how the MEP formalizes this trade-off, and Eli will show how process variation turns this sensitivity into a yield nightmare.

---

---

### Slide 6: The NTC Sweet Spot `[8:30–10:00]`

**Visual:** Energy vs. $V_{DD}$ curve with three marked zones. Annotate: the NTC knee at ~0.5V, the MEP at ~0.3V (preview for Omri), and deep sub-VT at ~0.25V. Add a second panel showing the parallelism concept: 1 nominal core vs. 10 NTC cores at same total power.

**Content (Dreslinski et al., 2010 — "Near-Threshold Computing: Reclaiming Moore's Law"):**

Dreslinski et al. (2010) is the paper that named and quantified the NTC regime. Its central argument: the sub-threshold community had been treating everything below $V_{th}$ as a single design space. There is in fact a sharp knee in the energy-performance curve — and NTC sits exactly at that knee.

**Measured numbers (65nm process):**

| Operating Point | $V_{DD}$ | Energy vs. nominal | Speed vs. nominal |
|----------------|----------|--------------------|-------------------|
| Nominal | 1.1V | 1× | 1× |
| NTC sweet spot | ~0.5V | **~10× better** | ~10× slower |
| Deep sub-threshold | ~0.25V | ~20× better | **~500–1000× slower** |

**Why does the curve have a knee here?**

At NTC, you are at the crossover between weak and strong inversion. The circuit is slow enough to harvest substantial energy savings from reduced $V_{DD}$, but the exponential delay law has not yet fully taken over. Below NTC, delay scales as $e^{-V_{DD}/nV_T}$ — a tiny further drop in $V_{DD}$ causes a massive increase in $T_{clk}$, which multiplies into the leakage energy term $I_{leak} \cdot T_{clk}$ and rapidly erases all dynamic savings.

The trade-off below NTC is catastrophic: **50–100× more slowdown for only 2× more energy savings**. For any system that needs to complete work in finite time, that is an unacceptable trade.

**The Massive Parallelism Solution — "Reclaiming Moore's Law":**

The 10× performance penalty of NTC sounds like a deal-breaker. Dreslinski's key insight is that it isn't — because the freed power budget can be reinvested in parallel cores.

- 1 nominal core at 1.1V: 1× throughput, 1× power.
- 10 NTC cores at 0.5V: ~1× total throughput (10 cores × 1/10 speed), ~10× less total energy.

**NTC lets you reclaim Moore's Law through energy efficiency rather than frequency scaling.** When the frequency wall hit ~4 GHz in 2004, the industry shifted to multi-core. NTC extends that logic: instead of 4 high-voltage cores, you run 40 NTC cores at a fraction of the energy per operation.

IBM's 2024 5nm SoC is the direct production realization of this principle — 19 years after Dreslinski's prediction.

**Killer Fact:** The 10× NTC performance penalty is fully recoverable by running 10 cores in parallel within the same power budget. Deep sub-VT offers no such recovery — 100× slowdown cannot be parallelized away at reasonable die area.

---

**Handoff note:** Hand over to Omri — he covers the MEP equation that shows *mathematically* why the knee in the energy curve sits where it does.

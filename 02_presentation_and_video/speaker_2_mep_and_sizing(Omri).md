# Speaker 2 (Omri): MEP Theory & Sizing
## Slides 7–10 | 10:00–18:00 (8 min)

---

## Timing Overview

| Slide | Topic | Time |
|-------|-------|------|
| Slide 7 | Dynamic Energy & The Quadratic Law | 10:00–12:00 |
| Slide 8 | Leakage Energy + The MEP | 12:00–14:30 |
| Slide 9 | The MEP is Not Fixed | 14:30–16:00 |
| Slide 10 | Sizing for Minimum Energy | 16:00–18:00 |

---

---

### Slide 7: Dynamic Energy & The Quadratic Law `[10:00–12:00]`

**Visual:** Animated build of the energy equation — first show only the dynamic term $\alpha C_{tot} V_{DD}^2$, with $V_{DD}$ on the x-axis and $E_{dyn}$ on the y-axis as a downward-opening parabola. Annotate each variable.

**Content (Calhoun et al., 2005):**

Eyal showed you *where* NTC sits on the energy-performance curve. My job is to show you *why* that curve has the exact shape it does — from first principles.

Every digital switching event charges or discharges a capacitor through the supply rail. The energy drawn from the supply per switching event is:

$$E_{dyn} = \alpha \cdot C_{tot} \cdot V_{DD}^2$$

Breaking down each term:
- $\alpha$ — the **activity factor**: the average fraction of nodes that switch per clock cycle. In typical logic $\alpha \approx 0.1$–$0.5$. This is the only software-controllable term.
- $C_{tot}$ — total switched capacitance: gate oxide capacitance + interconnect + drain junction. Scales with transistor count and wire length.
- $V_{DD}^2$ — the quadratic term: this is the key leverage. Halving $V_{DD}$ reduces dynamic energy by **4×**, not 2×.

This quadratic dependence is why voltage scaling is so powerful — and it holds regardless of operating regime. Whether you are in super-threshold or sub-threshold, the charge deposited on a capacitor is $Q = C \cdot V$ and the energy stored is $\frac{1}{2}CV^2$. The factor of 2 difference (stored vs. drawn) is accounted for in $\alpha$.

**The logical conclusion:** just keep reducing $V_{DD}$ forever, right? Not quite — and the next slide explains why.

---

---

### Slide 8: Leakage Energy + The MEP `[12:00–14:30]`

**Visual:** U-shaped energy curve with three labeled lines building up one at a time:
1. $E_{dyn}$ — downward-sloping parabola (from Slide 7)
2. $E_{leak}$ — rises steeply as $V_{DD}$ decreases (due to the delay blow-up)
3. $E_{tot} = E_{dyn} + E_{leak}$ — U-shaped, with the minimum clearly marked as "MEP ≈ 300mV" for 180nm

**Content (Calhoun et al., 2005):**

The problem with just reducing $V_{DD}$ is the **leakage energy term**:

$$E_{tot} = \underbrace{\alpha C_{tot} V_{DD}^2}_{E_{dyn}} + \underbrace{V_{DD} \cdot I_{leak} \cdot T_{clk}}_{E_{leak}}$$

$E_{leak}$ is leakage current multiplied by supply voltage multiplied by the time the circuit takes to finish one operation.

**The hidden exponential — circuit delay:**

In sub-threshold, the current driving a logic gate is $I_{sub} \propto e^{V_{DD}/nV_T}$. Since delay is inversely proportional to drive current:

$$T_{clk} \propto \frac{1}{I_{sub}} \propto e^{-V_{DD}/nV_T}$$

This is the trap: as you lower $V_{DD}$, the circuit slows down *exponentially*. A reduction from 500mV to 300mV does not double the delay — it multiplies it by $e^{(0.5-0.3)/(1.4 \times 0.026)} \approx 250\times$.

So $E_{leak} = V_{DD} \cdot I_{leak} \cdot T_{clk}$ initially drops as $V_{DD}$ falls (the $V_{DD}$ term dominates), but then turns around sharply as the exponential growth in $T_{clk}$ takes over. The sum $E_{tot}$ forms a U-shape with a well-defined minimum.

**The Minimum Energy Point:**

$$\frac{dE_{tot}}{dV_{DD}} = 0 \quad \Rightarrow \quad \text{MEP}$$

Calhoun et al. solved this analytically and measured the result on a **180nm test chip: MEP ≈ 300mV**. Below the MEP, you are actively wasting energy — the exponential delay increase causes leakage to dominate and total energy actually *rises*.

**Killer Fact:** At 100mV — less than a third of the MEP — the circuit leaks for so long that total energy per operation is *higher* than at the MEP. Going ultra-low-voltage does not mean ultra-low-energy.

**Q&A Prompt:** *What if we use a faster process node? Does the MEP move?*  
→ Yes — which is exactly the next slide.

---

---

### Slide 9: The MEP is Not Fixed `[14:30–16:00]`

**Visual:** Three overlapping U-curves on the same axes, each shifted — one for "Typical corner / 25°C", one for "Slow corner / 85°C", one for "Fast corner / -40°C". Show the MEP minimum sliding left and right across the curves. Add a vertical band labelled "Static design margin — one fixed $V_{DD}$" that misses all three minima.

**Content (Calhoun et al., 2005 + Zangi et al., 2018):**

The MEP equation contains $I_{leak}$ and the delay model — both of which depend heavily on three factors that change continuously during chip operation:

**1. Temperature:**
- $I_{leak} \propto e^{-E_a/kT}$ — leakage doubles roughly every 10°C.
- A chip running hotter has more leakage, which raises the leakage energy term and shifts the MEP *upward* in voltage.
- A chip going from 25°C to 85°C can shift the MEP by **50–100mV**.

**2. Process Corner ($V_{th}$ spread):**
- In the "Slow" corner, $V_{th}$ is higher than nominal — transistors are slower, $T_{clk}$ is longer, leakage energy dominates earlier, so the MEP is higher.
- In the "Fast" corner, the opposite: MEP shifts lower.
- Across a typical PVT envelope, the MEP can range across **~150mV**.

**3. Aging:**
- Hot Carrier Injection (HCI) and Bias Temperature Instability (BTI) gradually increase $V_{th}$ over years of operation — slowly pushing the MEP upward over the chip's lifetime.

**The static design-margin problem:**

A chip taped out at one fixed $V_{DD}$ must cover all these scenarios. The only safe choice is to target the worst-case MEP (Slow corner, hot, aged) — which is 100–200mV higher than the typical-corner MEP. This erases most of the energy benefit of sub-threshold operation.

This is the fundamental motivation for Eli's section: you need a closed-loop system that tracks the MEP *in real time* rather than baking in a static margin at design time.

---

---

### Slide 10: Sizing for Minimum Energy `[16:00–18:00]`

**Visual:** Two-panel comparison:
- Left: standard CMOS inverter with width $4W_{min}$ — annotate increased gate capacitance and wider leakage footprint.
- Right: minimum-sized inverter $W_{min}$ — annotate reduced capacitance and leakage.
- Bar chart below: $E_{dyn}$, $E_{leak}$, and $T_{clk}$ for both sizes. Show that upsizing helps $T_{clk}$ slightly but hurts both energy terms significantly.

**Content (Calhoun et al., 2005):**

In standard above-threshold design, upsizing a transistor (increasing $W$) is a routine tool: more width → more drive current → faster switching → tighter timing closure. The area and leakage penalty is acceptable because $I_{on}$ scales linearly with $W$ and the speed gain is proportional.

**In sub-threshold, this logic completely inverts:**

- **Capacitance scales linearly with $W$:** $C_{gate} = C_{ox} \cdot W \cdot L$. Doubling $W$ doubles $E_{dyn}$ directly.
- **Leakage scales linearly with $W$:** $I_{leak} \propto W/L$. Doubling $W$ doubles $E_{leak}$.
- **Speed improves sub-linearly:** In sub-threshold, $I_{sub}$ is dominated by the exponential $e^{(V_{GS}-V_{th})/nV_T}$ — the linear $W$ factor is small relative to the exponential term. The speed gain from doubling $W$ is far less than 2×.

The result: **every increase in $W$ raises both energy terms while delivering diminishing speed returns.** $W_{min}$, $L_{min}$ is always the optimal sizing strategy for sub-threshold circuits.

**Calhoun's finding:** On the 180nm test chip, minimum-sized gates achieved the lowest total energy per operation at the MEP. No upsizing of any gate improved the energy-delay product.

**The FinFET/GAAFET Caveat — an open research gap:**

Calhoun's 2005 result was derived on a **planar 180nm process** where $W$ is a continuous variable. In modern FinFET (7nm, 5nm) and GAAFET (3nm, 2nm) nodes, transistor width is **quantized** — you can only increase width by adding fins or nanosheets, each adding a discrete increment of capacitance and current. The smooth $W_{min}$ optimum may not exist in the same way. Whether Calhoun's sizing rule holds at sub-threshold in a quantized-width FinFET process is an **unresolved research question** — and one of the open gaps our Phase 04 proposal will address.

**Killer Fact:** In sub-threshold, upsizing a transistor to save 10% on delay costs you 100% more capacitance and 100% more leakage. It is always a losing trade.

---

**Handoff note:** Hand over to Eli — the MEP is the target, but Slide 9 showed it shifts with temperature and process. Eli explains exactly *why* that variability is so destructive at the exponential operating point, and how Zangi's ADVC solves it.

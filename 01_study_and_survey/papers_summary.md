# Literature Survey: Sub-Threshold & Near-Threshold Computing

Detailed summaries of the 5 papers in this folder, supporting the project theme:
**"Operating circuits at extreme low voltages for maximum energy efficiency."**

Each entry is written from a full read of the PDF and is structured for the
presentation: *what problem it solves → the core idea → key results → why it matters
for us.* The papers form a logical arc:

> **Theory of the optimum (1)** → **the practical "sweet spot" (2)** → **a complementary
> efficiency axis (5)** → **a production silicon solution to variability (3)** →
> **a modern edge-AI use case (4).**

---

## 1. Modeling and Sizing for Minimum Energy Operation in Sub-threshold Circuits
*B. H. Calhoun, A. Wang, A. Chandrakasan — IEEE JSSC, Vol. 40, No. 9, Sept. 2005*
[DOI: 10.1109/JSSC.2005.852162](https://doi.org/10.1109/JSSC.2005.852162) ·
[Local PDF](./Modeling_and_sizing_for_minimum_energy_operation_in_subthreshold_circuits.pdf)

**Problem.** For energy-constrained apps (sensor nodes, medical implants) where speed
is secondary, what supply voltage minimizes energy per operation in the sub-threshold
region — and does shrinking transistors help?

**Core idea — the Minimum Energy Point (MEP).** Total energy per cycle is the sum of
two competing terms:
- **Dynamic energy** `E_dyn = C_eff · V_DD²` — falls quadratically as V_DD drops.
- **Leakage energy** `E_leak = W_eff·I_0·K·L_DP·V_DD²·e^(−V_DD / nV_th)` — *rises* as
  V_DD drops, because the circuit slows exponentially and leaks for longer each cycle.

The sum has a minimum. The paper differentiates the total-energy equation and solves
it **analytically** using the Lambert-W function:

> **V_DDopt = n·V_th · (2 − lambertW(β))**, where β depends on the ratio of dynamic to
> leakage energy (C_eff, W_eff, logic depth L_DP).

**Key findings.**
- The analytical MEP matched a numerical transregional model and SPICE within **<0.1%**
  for an 8-bit/8-tap FIR filter — optimum ≈ **250 mV at 30 kHz**; an FFT processor sat
  at ≈ 350 mV.
- **V_DDopt is independent of frequency and of V_T**; it is set purely by the *relative*
  weight of dynamic vs. leakage energy. More idle width or deeper logic → higher MEP.
- High-activity circuits (e.g. a ring oscillator) have *no* interior minimum — leakage
  never dominates, so the optimum is simply the lowest functional voltage.
- The MEP **moves by several hundred mV** with workload, duty cycle and temperature
  (≈ +100 mV over the full temperature range) → argues for **closed-loop tracking**.

**Sizing result.** Matching pMOS/nMOS current (P/N ≈ 12 in this 0.18 µm node) lets a
ring oscillator run down to ~70 mV, but up-sizing every pMOS 12× to gain only ~0.6×
voltage savings is not worth it. **Minimum-sized devices are theoretically optimal for
energy.** A 0.18 µm test chip confirmed standard CMOS cells work down to **150 mV**;
existing standard-cell libraries are already a good fit for sub-threshold, and the
shallow MEP means restricting/resizing the cell set buys little (≤10% at worst corner,
+50% overhead typically).

**Why it matters.** This is the **foundational physics and math** of the project — it
defines the MEP, proves minimum sizing is optimal, and motivates adaptive voltage
tracking (which paper 3 implements in silicon).

---

## 2. Near-Threshold Computing: Reclaiming Moore's Law Through Energy-Efficient ICs
*R. G. Dreslinski, M. Wieckowski, D. Blaauw, D. Sylvester, T. Mudge — Proc. IEEE,
Vol. 98, No. 2, Feb. 2010 (Invited)*
[DOI: 10.1109/JPROC.2009.2034764](https://doi.org/10.1109/JPROC.2009.2034764) ·
[Local PDF](./Near-Threshold_Computing_Reclaiming_Moores_Law_Through_Energy_Efficient_Integrated_Circuits.pdf)

**Problem.** Deep sub-threshold gives the best energy but pays a brutal speed price, so
it stayed in niche markets (watches, hearing aids). Meanwhile post-65 nm scaling stopped
delivering energy gains and power budgets now strand transistors ("dark silicon").

**Core idea — the NTC sweet spot.** Run at **V_DD ≈ V_th (~400–500 mV)** instead of
deep sub-threshold (~250–350 mV). Scaling from nominal 1.1 V down to NTC gives ~**10×
energy** reduction for ~**10× delay** — a far better trade than pushing into sub-threshold,
where you gain only ~2× more energy but lose another **50–100×** in speed. Measured on the
Subliminal and Phoenix sub-threshold processors: NTC delivered ~6.6–9.8× energy savings
with workable clock rates.

**The three NTC barriers (and the fixes the paper surveys):**

| Barrier | Magnitude at ~400 mV | Mitigations discussed |
|---|---|---|
| **Performance loss** | ~10× slower (FO4) | Massive **cluster-based parallelism** (10s–100s of NTC cores sharing a faster L1 cache, since SRAM's V_min is ~100 mV above logic); device optimization (channel-doping tweaks → 44% faster; **FDSOI**; modified scaling). |
| **Performance variation** | global σ/µ up to ~5×; total uncertainty up to ~20× | **Soft-edge clocking** (transparency window for time-borrowing, +11.7% perf in a FIR filter); **adaptive body biasing** (ABB) — tunes P/N strength, improved V_min by 24%, also minimizes energy. |
| **Functional failure** | 65 nm SRAM bitcell failure 10⁻⁷ → ~4% (5 orders) | **8T / 10T SRAM** cells that decouple read/write; single-ended 6T with column supply modulation; importance-sampling for iso-robustness sizing; reconfigurable caches. |

**Future direction.** *In-situ* delay monitoring with transition detectors + a
worst-case-vector table to adapt V/f per "environmental epoch" — beyond canary circuits
and Razor (which struggle in NTC due to large speculation windows).

**Why it matters.** Provides the **conceptual framework** for the whole project: it names
the regions (sub-/near-/super-V_th), quantifies the energy-delay landscape (the Fig. 2
the team should reproduce), and catalogs the variability/robustness problems that papers
3, 4, and 5 each attack from a different angle.

---

## 3. 0.45 V and 18 µA/MHz MCU SoC with Advanced Adaptive Dynamic Voltage Control (ADVC)
*U. Zangi, N. Feldman, T. Hadas, N. Dayag, J. Shor, A. Fish — JLPEA, Vol. 8, No. 2, 2018
(PLSense + Bar-Ilan)*
[DOI: 10.3390/jlpea8020014](https://doi.org/10.3390/jlpea8020014) ·
[Local PDF](./jlpea-08-00014-v2.pdf)

**Problem.** Prior sub-/near-threshold research showed big power savings but **no
complete, mass-producible MCU SoC** that holds up across process corners, temperature,
and aging at low voltage.

**Core idea — a holistic, cross-layer SoC with closed-loop voltage tracking.** Built in
**40 nm ULP CMOS** around a complete Synopsys ARC EM5D core (full DSP ISA), operating
**0.45–1.1 V**, 312 kHz–80 MHz, directly off a 1.6–3.6 V battery. Optimization spans
*every abstraction layer*:

- **Custom low-voltage libraries** (~300 cells). Use **mixed-V_t (HVT+SVT) transistors
  inside the same cell** to symmetrize the VTC and speed up weak pMOS; selective bulk
  biasing; no high fan-in; non-ratioed flip-flops. Result: **50% variance reduction** and
  better static noise margins vs. a conventional cell (which shifts 120 mV FS→SF at 450 mV).
- **ADVC loop** (the headline contribution): on-die sensors (process monitor, temperature,
  aging, V_t) feed a **look-up table** that sets a **VID** code; the PMU then sets the
  exact core voltage (10 mV steps) for the user's target frequency *and* the actual
  silicon/temperature conditions. This goes beyond plain DVFS — it also tracks
  environment, improving **yield and lifetime**, not just matching V to f.
- **First-Fail (FFail) circuit** — a programmable critical-path monitor. A programmable
  **in-line DLL** is calibrated to replicate the true critical-path delay, then used as a
  canary that flags the lowest safe voltage for a given frequency (8-bit / 511-step
  control). Measurements show FFail code tracks core frequency accurately and works down
  to **0.45 V at 2 MHz**.
- **Power management** — two inductor-based DC-DC converters (>85% efficiency even at low
  V/low load) using Pulse-Skipping Modulation for light loads; LDOs, bandgap, 12-bit SAR
  ADC.

**Results.** Peak efficiency **18 µW/MHz**; vs. commercial Cortex-M4F MCUs, **~3× better
current/DMIPS** and ~2× better current/MHz; at <2 MHz / 0.45–0.5 V the gap widens to
3–4×. Next-gen PLS15 projected to nearly double the advantage.

**Why it matters.** This is the **practical implementation paper** — it shows how the MEP
theory (paper 1) and the variability problems (paper 2) are actually solved in production
silicon via sensors + LUT + FFail canary + adaptive voltage/body-bias. Strong, concrete
demo for the "how it's done in industry" portion of the talk.

---

## 4. LAXOR: A Bit-Accurate BNN Accelerator with Latch-XOR Logic for Local Computing
*D. Li, T. Yamasaki, A. Mani, A. T. Do, N. Chen, B. Wang — IEEE/ACM ISLPED 2023 (SUTD +
A*STAR Singapore)*
[DOI: 10.1109/ISLPED58423.2023.10244499](https://doi.org/10.1109/ISLPED58423.2023.10244499) ·
[Local PDF](./LAXOR_A_Bit-Accurate_BNN_Accelerator_with_Latch-XOR_Logic_for_Local_Computing.pdf)

**Problem.** Binary Neural Networks (BNNs) are ideal for edge/AIoT (tiny models,
XNOR+popcount instead of multiply-accumulate). Two existing approaches each have a flaw:
**analog/mixed-signal compute-in-memory** is efficient but inaccurate (sensitive to noise,
temp, process), while **all-digital** designs keep weight storage and the XOR gate
*separate*, wasting energy on constant data movement.

**Core idea — fuse storage and compute.** A custom **10-transistor Latch-XOR cell** does
in-situ bitwise multiply: a cross-coupled latch holds the weight, and a 2-transistor
path + inverter performs (weight ⊕ input) right where the weight lives. The IN line is
time-multiplexed for weight-load and data, saving area.
- **4.2× lower energy/op** (0.785 fJ vs. 3.30 fJ) and **5× smaller area** than the
  decoupled standard-cell latch+XOR.
- Verified by 10,000-point Monte-Carlo (3σ); fully functional at **0.5 V, 200 MHz**
  with SVT devices (HVT fails at that corner) — tying the design back to the low-voltage
  theme.

**Other contributions.**
- **PCL popcount** (parallel-counter + carry-look-ahead hybrid) for accumulation:
  **1.45× / 6.51× better EDA** and 1.37× / 1.96× lower power than Binary-Adder-Tree and
  LUT approaches.
- **Many-core architecture**: 4 clusters × 64 PEs, each PE = 1024 Latch-XOR cells +
  popcount + bias, with a flexible dataflow mapping for various kernel sizes.
- An open-source **Python cycle-accurate simulator** (Areca front-end + Bits-Island
  back-end) for HW/SW co-design that models **leakage energy** (critical at low V),
  ~8×10⁶ faster than Spectre.

**Results.** **2315 TOPS/W** in 28 nm — **3.4×** better than the best synthesized-digital
BNN accelerator (up to 37.8× vs. others); CIFAR-10 at 85.25% / 3.82 µJ per inference
(1.8× lower energy than the SOTA digital baseline), and **bit-accurate** (no accuracy
loss from analog non-ideality).

**Why it matters.** The **modern industry use-case**: shows the low-voltage / energy-first
philosophy applied to today's hottest workload (edge AI), and reinforces a recurring
project theme — **minimizing data movement and switching activity** beats raw device
speed for energy.

---

## 5. Near/Sub-Threshold Circuits and Approximate Computing: The Perfect Combination for Ultra-Low-Power Systems
*J. Schlachter, V. Camus, C. Enz — IEEE ISVLSI 2015 (EPFL ICLAB)*
[DOI: 10.1109/ISVLSI.2015.41](https://doi.org/10.1109/ISVLSI.2015.41) ·
[Local PDF](./Near_Sub-Threshold_Circuits_and_Approximate_Computing_The_Perfect_Combination_for_Ultra-Low-Power_Systems.pdf)

**Problem.** Sub-/near-threshold gives minimal energy but worsens speed, area, and PVT
variability. Can a *complementary* technique relax those constraints instead of just
scaling voltage further?

**Core idea — pair low-voltage with approximate computing.** For error-tolerant workloads
(DSP, image/video, ML), trade a little accuracy for big savings in area, delay, and power.
The paper reviews and *combines* two automatic techniques for arithmetic circuits:
- **Gate-level pruning** — represent the netlist as a DAG; rank gates by
  **Significance × Activity Product (SAP)** and remove the lowest first via one extra step
  in the standard flow. Errors concentrate in the LSBs (low magnitude, high rate).
- **Inexact Speculative Adders (ISA)** — split the carry chain into concurrent
  SPEC→ADD→COMP segments; speculate carries from a few bits (carry sequences are usually
  short) and compensate faults. Errors are rare but can hit MSBs.

**Key result — the techniques are additive.** Because pruning and speculation produce
**uncorrelated** errors (LSB-concentrated vs. rare-but-anywhere), combining ("mixed"
adders) stacks the savings. On 32-bit adders (65 nm UMC, 5 M random inputs):
- Each technique alone: up to **85% PDAP reduction** at 1% RMS relative error.
- **Mixed**: a pruned-speculative adder hits **PDAP ≈ 0.05 (≈20× saving in EDAP)** at just
  2% RMS error, and **~4× lower energy** than the exact adder at the same speed.
- Demonstrated on a **180 nm sub-threshold library** (22.7 MHz): pruned sub-threshold
  adders show the same savings → approximate design directly mitigates sub-threshold's
  area/variability/speed penalties.

**Why it matters.** Opens a **second innovation axis** beyond pure voltage scaling: the
energy savings from approximation **multiply** on top of the quadratic V_DD reduction, and
shorter critical paths counter low-voltage delay and variability. A clean conceptual
counterpoint to the *bit-accurate* approach of LAXOR (paper 4) — useful contrast slide.

---

## Cross-paper threads (good slide material)

- **The Minimum Energy Point is the spine** — defined analytically (1), located as the
  NTC sweet spot (2), tracked in production silicon (3).
- **Variability is the enemy of low voltage** — quantified (2), beaten with custom
  libraries + sensors + ADVC/FFail (3), sidestepped with approximation (5), or avoided
  with bit-accurate digital design (4).
- **Energy ≠ speed** — for these workloads, cutting switching activity and data movement
  (1's minimum sizing, 4's fused Latch-XOR, 5's pruning) matters more than device drive.
- **Timeline**: 2005 theory → 2010 framework → 2015 approximate-computing axis → 2018
  production SoC → 2023 edge-AI accelerator. A neat "evolution of the field" narrative.

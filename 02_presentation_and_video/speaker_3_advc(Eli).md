# Speaker 3 — Adaptive Dynamic Voltage Control / ADVC (Eli)

**Topic:** Tracking the optimum in real silicon — *How a production chip senses its own speed and runs at the lowest safe voltage instead of a fixed worst-case margin.*
**Time:** 20:00 – 30:00 (10 min slides, ~12 min with Q&A)
**Papers owned:** [3] Low-power MCU SoC with **Adaptive Dynamic Voltage Control (ADVC)** (production-silicon paper, ~2018).
**Goal:** Show that the MEP/optimum Omri located is not a fixed number — it drifts with process, voltage, temperature, and aging — and that real chips *sense and track* it live to delete the worst-case voltage margin.

> **Scope note:** Omri owns the MEP math and the dual-voltage-domain idea; Nadav owns the bit-accurate edge-AI accelerator. I own the *sensing-and-tracking* hardware: process monitors, the in-line DLL, and the First-Fail circuit that finds the minimum safe voltage on the actual die.

---

## Slide 1 — The Article (20:00 – 20:20)

**On screen**
- The source paper: a low-power MCU SoC. **Focus of this talk: the ADVC technology — Adaptive Dynamic Voltage Control.**
- Core question: how can the chip run at very low power *without* simply picking one fixed, conservative voltage?

**Speaker notes**
- Set expectations: the paper is a whole SoC, but we zoom in on one block — ADVC — because it's the direct answer to Omri's "the optimum drifts" problem.

---

## Slide 2 — Presentation Focus: What ADVC Does (20:20 – 20:55)

**On screen**
- ADVC's core idea: the chip should **not** always run at a fixed worst-case voltage.
- Instead: pick a target **frequency**, and ADVC finds the **minimum safe voltage** for that frequency.
- That voltage depends on the **actual chip** — process variation, temperature, aging, and on-die variation — not just the datasheet.
- Net effect: **remove unnecessary voltage margin.**

**Speaker notes**
- The one-sentence definition: ADVC is closed-loop removal of guardband. Everything else is *how* it measures the chip so it can trust a lower voltage.

---

## Slide 3 — Why ADVC Is Needed (20:55 – 21:35)

**On screen**
- Lowering voltage is one of the best ways to cut power — but lower voltage makes gates **slower**.
- Too slow → a signal misses the next flip-flop → **timing failure.**
- Chips aren't identical: some are faster, some slower; the same chip changes with temperature and aging.
- Without ADVC you must pick a voltage that works in the **worst case** — and pay that cost always.

**Speaker notes**
- Tie straight back to Eyal's variability con and Omri's "optimum drifts." Worst-case margin is the tax; ADVC's job is to stop paying it when conditions are better than worst-case.

---

## Slide 4 — High-Level ADVC Flow (21:35 – 22:20)

**On screen**
- The application chooses the **frequency** it wants.
- ADVC decides what **voltage** that frequency needs, using: process corner, temperature, aging, on-die variation, and **First-Fail** results.
- ADVC outputs a **VID** — the code that tells the PMU which voltage to generate.
- Order of operations: **frequency first → minimum safe voltage second.**

**Speaker notes**
- Stress the direction of causality: frequency is the input, voltage is the *computed* output. This is the opposite of fixed-voltage design.

---

## Slide 5 — Sensors & Process Monitor (22:20 – 23:00)

**On screen**
- Sensors determine the **process condition** of *this specific* chip, plus temperature, aging, etc.
- They're **distributed** across the die — because the die isn't uniform; one region can be hotter or slower than another.
- The **Process Monitor** is a **ring oscillator** built from various standard cells: faster oscillation → faster silicon; slower → slower silicon.

**Speaker notes**
- Same ring-oscillator trick Omri used to find functional V_DD — here it's repurposed as a live "how fast is this silicon?" gauge.

---

## Slide 6 — In-Line DLL Calibration (23:00 – 23:50)

**On screen**
- The **in-line DLL** measures the *actual* critical-path delay — it's wired directly to the real critical path, not a replica.
- This is a **calibration** step; it does **not** run live alongside First-Fail.
- Output: the real critical-path delay, expressed as a **DLL code.**

**Speaker notes**
- Key distinction to make clear: "in-line" = connected to the genuine critical path, so the measurement reflects the real chip, not an approximation.

---

## Slide 7 — How the DLL Code Is Found (23:50 – 24:50)

**On screen**
- Turn the critical path into an **oscillation loop**; its frequency corresponds to the critical-path delay.
- Add the **programmable DLL** into the loop → now the loop = critical-path delay + DLL delay.
- **Binary-search** the DLL code until the frequency is **half** the original.
- Half frequency ⇒ delay doubled ⇒ the DLL delay now **equals** the critical-path delay → that DLL code *represents* the critical path.

**Speaker notes**
- This is the cleverest slide — walk the logic slowly. Doubling the period by adding the DLL means the DLL's contribution matches the path's contribution exactly. That's how a *programmable delay* gets calibrated to the *real* path.

---

## Slide 8 — The First-Fail Circuit (24:50 – 25:35)

**On screen**
- First-Fail uses the **DLL code** found during calibration — a programmable delay that *represents* the critical path.
- It does **not** use the in-line DLL live.
- It's a small **timing-test circuit** that emulates the chip's timing behavior.
- Purpose: find the **lowest voltage** where that critical-path-like delay still works at the selected clock frequency.

**Speaker notes**
- Reframe: First-Fail is a safe, cheap "canary" copy of the critical path you can push to failure without crashing the real chip.

---

## Slide 9 — How First-Fail Works (25:35 – 26:25)

**On screen**
- Two flip-flops with the **calibrated delay** between them.
- FF1 launches a **1** → it travels through the delay → FF2 tries to sample it.
- Time available = the current **clock period**.
- Delay short enough → FF2 captures **1** (pass). Too long → captures **0** (fail).
- → a simple **pass/fail** timing result.

**Speaker notes**
- It's literally a launch/capture race. Pass means "the path made it in time at this voltage and frequency"; fail means it didn't.

---

## Slide 10 — Local Voltage for First-Fail (26:25 – 27:05)

**On screen**
- First-Fail has its **own local voltage**, controlled via the power-management path (likely the LDO).
- Changing this local voltage changes the **delay** of the buffers / DLL elements:
  - Lower voltage → longer delay. Higher voltage → shorter delay.
- So sweeping the First-Fail voltage finds the point where the delay **just barely fits** in the clock period.

**Speaker notes**
- The local rail is what makes the canary *tunable* — you can dial its voltage down independently to find the edge without disturbing the rest of the chip.

---

## Slide 11 — Finding the Minimum Safe Voltage (27:05 – 27:50)

**On screen**
- The chip is running at a chosen frequency; First-Fail is loaded with the critical-path DLL code.
- Sweep the First-Fail local voltage:
  - **Fail** → too low → raise voltage.
  - First **pass** → voltage is just high enough for this frequency and condition.
- That voltage is then applied to the rest of the chip.

**Speaker notes**
- This is the closed loop in one slide: test → adjust → settle at the first passing voltage. That first-pass voltage is the minimum safe voltage, margin removed.

---

## Slide 12 — Multiple First-Fail Sensors (27:50 – 28:25)

**On screen**
- Several First-Fail sensors are placed **around** the die.
- The die isn't uniform — one region may be slower, hotter, or more variable than another.
- ADVC reads all sensors and picks the **worst-case** result → a safe voltage for the **whole** chip.

**Speaker notes**
- Echo of Omri's on-die variation: you trust the slowest corner *measured on this die*, which is still far better than the slowest corner *of all possible dies*.

---

## Slide 13 — Main Takeaway (28:25 – 29:05)

**On screen**
- ADVC is **not** simple voltage scaling — it's a **feedback-based** voltage control system:
  - Application chooses frequency.
  - Sensors determine process + environment.
  - In-line DLL measures the real critical-path delay → a code.
  - First-Fail uses the code to test timing and find the minimum safe voltage.
- Result: run at low voltage **without** a large fixed worst-case margin.

**Speaker notes**
- This is the slide to land if time is short. The whole section reduces to: *measure the real chip, then trust a lower voltage.*

---

## Slide 14 — Low-Power Chips: A Question (29:05 – 29:30)

**On screen** *(audience question)*
- A chip with an integrated sensor sits in a field monitoring soil moisture. It sleeps most of the time, wakes every few hours to measure, transmits wirelessly, and sleeps again.
- We can choose **1 MHz** or **10 MHz**. **Which is more energy efficient?**

**Speaker notes**
- Pose it, pause, take a guess or two — this doubles as the section's Kahoot hook before the reveal on the next slide.

---

## Slide 15 — Low-Power Chips: The Answer (29:30 – 30:00)

**On screen**
- **It depends.**
- *Without ADVC (fixed voltage):* lower frequency lowers power but the task takes longer; energy = power × time, so dynamic energy is similar — and the longer awake time burns **more static energy**. → usually better to **finish fast and sleep** ("race to sleep").
- *With ADVC:* at lower frequency ADVC can also **lower the voltage**, and dynamic power goes with **V²** — so a lower frequency can become **more** energy efficient.

**Speaker notes**
- The punch line ties the whole section to Eyal's V² law: ADVC turns "slower" into "lower voltage," which turns the usual race-to-sleep intuition on its head.
- Handoff to Nadav: "We can now *track* the optimum on real silicon. Nadav will show what we actually *run* on these chips — a robust, bit-accurate edge-AI accelerator — and then close the talk."

---

## Kahoot Questions (Speaker 3)

**Q1.** What does ADVC (Adaptive Dynamic Voltage Control) actually do?
- A) Picks one fixed worst-case voltage for all chips
- ✅ **B) For a chosen frequency, finds the minimum *safe* voltage for the actual chip, removing unnecessary margin**
- C) Raises the clock frequency as high as possible
- D) Disables the SRAM to save power

**Q2.** The Process Monitor decides whether the silicon is fast or slow by…
- A) Reading a fixed value from the datasheet
- ✅ **B) Measuring a ring oscillator built from standard cells — faster oscillation means faster silicon**
- C) Counting the number of flip-flops
- D) Measuring the battery voltage

**Q3.** In the DLL calibration, the DLL code represents the critical-path delay when the loop frequency becomes…
- A) Double the original frequency
- ✅ **B) Half the original frequency — meaning the delay doubled, so the DLL delay equals the critical-path delay**
- C) Exactly the clock frequency
- D) Zero

**Q4.** How does the First-Fail circuit report a timing problem?
- A) It measures temperature directly
- ✅ **B) FF1 launches a 1 through the calibrated delay; if FF2 captures 1 it passes, if it captures 0 it fails**
- C) It counts dropped network packets
- D) It compares two SRAM cells

**Q5.** Why does ADVC place **multiple** First-Fail sensors across the die and take the worst result?
- A) To save area
- ✅ **B) The die isn't uniform — some regions are slower/hotter, so the worst-case sensor gives a safe voltage for the whole chip**
- C) Because one sensor is always broken
- D) To increase the clock frequency

---

## Visuals to prepare
- **ADVC feedback loop:** application frequency → sensors + First-Fail → VID → PMU → chip voltage → back to sensors. *(Anchor diagram.)*
- **DLL calibration:** critical path as an oscillation loop, then with the programmable DLL inserted; binary search to half-frequency. *(Slide 7.)*
- **First-Fail cell:** FF1 → calibrated delay → FF2, launch-a-1/capture race against the clock period. *(Slide 9.)*
- **On-die sensor map:** several First-Fail / process-monitor sensors scattered across the die, "take the worst." *(Slide 12.)*
- **Race-to-sleep vs. ADVC:** energy bars for 1 MHz vs. 10 MHz, with and without voltage scaling. *(Slides 14–15.)*

> **Coordination:** I own ADVC's sensing/tracking hardware (process monitor, in-line DLL, First-Fail). I do **not** re-derive the MEP (Omri) or cover LAXOR (Nadav). The ring-oscillator and on-die-variation ideas deliberately echo Omri so the talk feels continuous.

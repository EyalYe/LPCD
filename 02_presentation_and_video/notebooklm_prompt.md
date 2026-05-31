# NotebookLM Master Prompt
## Phase 02: Presentation + Video — "Operating Circuits at Extreme Low Voltages"
### Course: Low Power Digital VLSI Circuits Design 83946 | Bar-Ilan University | Prof. Alexander Fish

---

## STEP 0: SOURCES TO LOAD INTO THIS NOTEBOOK

Before running any prompt below, load the following documents as sources:

1. `01_study_and_survey/papers_summary.md` — Top 5 paper citations and core contributions
2. `01_study_and_survey/total_research_summary.md` — Technical data, equations, and experimental results
3. `01_study_and_survey/research_notes.md` — Foundational physics and challenge analysis
4. `01_study_and_survey/session01_wrapup.md` — Full per-paper summary with synthesis
5. `02_presentation_and_video/presentation_final.md` — Complete slide-by-slide plan (use as the structural backbone)

These five documents are your ground truth. Do not introduce technical claims, numbers, or equations that are not supported by at least one of these sources.

---

---

# TASK A: CLASS PRESENTATION

## A1. Context & Constraints

**Who we are:** A team of 3 engineering students at Bar-Ilan University presenting to our class as part of the course "Low Power Digital VLSI Circuits Design 83946." In this course, student groups are the lecturers — we fully own our topic and are responsible for teaching it to our peers.

**Who the audience is:** Engineering students who have already studied, in this same course:
- Dynamic power ($P_{dyn} = \alpha C V_{DD}^2 f$), short-circuit power, and static leakage power
- Clock gating and power gating techniques
- Dynamic Voltage and Frequency Scaling (DVFS)
- Nanometer transistor models (including FinFET and, conceptually, the 14Å process)
- Leakage reduction at the circuit and system level

They have NOT studied sub-threshold or near-threshold computing. We are the source for this topic.

**The goal:** Teach the class sub-threshold and near-threshold computing from the ground up, in an engaging, rigorous, peer-to-peer lecture format. We want them to leave understanding the physics, the math, the silicon solution, and where the industry is today — and to be able to answer the Kahoot questions without re-reading anything.

**Hard constraints:**
- Total time: 45 minutes
- Presentation: ~33 minutes (10 slides)
- Kahoot: ~12 minutes (6 questions at the end)
- 3 presenters with equal speaking time (~11 minutes each)
- Slide count: exactly 10 slides + Kahoot section
- Must cite all 5 source papers explicitly at least once

---

## A2. Presenter Assignments

| Presenter | Slides | Time Window | Section |
|-----------|--------|-------------|---------|
| Presenter 1 | Slides 1–3 | 0:00–10:00 | Title, Motivation, Operating Regions |
| Presenter 2 | Slides 4–7 | 10:00–26:00 | MEP, Sizing, Variability+SRAM, ADVC |
| Presenter 3 | Slides 8–10 | 26:00–33:00 | LAXOR, IBM 5nm, Conclusion |
| Presenter 3 hosts | Kahoot | 33:00–45:00 | 6 questions |

Each presenter's section must open with a clean handoff line so transitions feel rehearsed, not awkward.

---

## A3. Slide-by-Slide Requirements

### Slide 1 — Title Slide (Presenter 1, 0:00–1:00)
- Title: "Operating Circuits at Extreme Low Voltages"
- Subtitle: "Maximizing Energy Efficiency Through Sub-Threshold and Near-Threshold Computing"
- Course name and group member names (placeholders: [Name 1], [Name 2], [Name 3])
- Opening hook line for Presenter 1 to deliver before advancing to Slide 2.
- Tone: confident, slightly dramatic. This is a "big idea" topic.

---

### Slide 2 — Motivation: Why Extreme Low Voltage? (Presenter 1, 1:00–5:00)
**Required content:**
- Connect explicitly to what the class already knows: "We've covered clock gating. We've covered power gating. We've covered DVFS. Those are all tools for managing power reactively — you either slow down or turn off. Today we go further."
- Three concrete motivation pillars (each gets a visual and a sentence):
  1. **Always-On IoT:** A buried bridge sensor or a smart-city node cannot be recharged. It must run continuously on harvested energy.
  2. **Biomedical Implants:** A neural interface or cardiac monitor cannot have its battery replaced. It must operate on microwatts for years.
  3. **The 14Å Thermal Wall:** At the 5nm/14Å process node, power density is so high that if all blocks ran at nominal voltage (1.1V), the chip would exceed the thermal envelope of its package. NTC is not optional — it is a thermal necessity. (Source: IBM 2024)
- Killer fact to deliver aloud: "Sub-threshold computing is no longer a niche for sensors — it is a production requirement in IBM's 5nm AI SoC."
- Anticipated student question to prep for: *"Why not just use aggressive power gating instead?"* Answer: power gating turns the system off. Sub-threshold enables continuous always-on operation at the physical minimum energy per operation.

---

### Slide 3 — The Operating Regions (Presenter 1, 5:00–10:00)
**Required content:**
- A table or visual comparing the three regimes:
  | Regime | VDD | Current | Speed | Energy vs. Nominal |
  |--------|-----|---------|-------|--------------------|
  | Super-threshold | >> Vth | Drift (quadratic) | GHz | 1× |
  | Near-threshold (NTC) | ≈ Vth | Mixed | 10–100 MHz | ~10× better |
  | Sub-threshold | < Vth | Diffusion (exponential) | kHz–MHz | ~20× better |
- The sub-threshold current equation: I_sub = I_0 * exp((V_GS - V_th) / n*V_T) * (1 - exp(-V_DS/V_T))
- Emphasize the exponential nature: "Every millivolt counts twice as much as in standard logic."
- **The 10×/10× rule from Dreslinski et al. (2010):** Scaling from 1.1V to 0.5V (NTC) gives 10× energy improvement with 10× performance degradation. Going deeper to 0.25V only adds 2× more energy savings but causes 100× more slowdown. NTC is the practical sweet spot.
- The system-level solution to the 10× performance loss: massive parallelism — run many NTC cores at the same power budget as one nominal core.
- Cite: Dreslinski et al., 2010, Proceedings of the IEEE.
- Anticipated question: *"Will entire complex processors run in sub-VT?"* Answer: heterogeneous designs — always-on inference blocks in sub-VT, burst-compute blocks at standard voltage.

**Handoff line to Presenter 2:** Something like: "Now that we've established where we want to operate, [Presenter 2] will walk us through the math that tells us exactly *how low* to go."

---

### Slide 4 — The Minimum Energy Point (Presenter 2, 10:00–15:00)
**Required content:**
- The MEP energy equation: E_tot = α*C_tot*V_DD² + V_DD * I_leak * T_clk
- Explain both terms and their opposing behavior as V_DD decreases:
  - E_dyn drops quadratically — good
  - T_clk grows exponentially in sub-VT — bad, because the circuit leaks for longer
  - The MEP is the V_DD where dE_tot/dV_DD = 0
- State the measured MEP: ~300mV in 180nm (Calhoun et al., 2005)
- Killer fact: "Operating *below* the MEP wastes energy. Going to 100mV instead of 300mV actually increases total energy consumed."
- Visual: the classic U-shaped E_tot curve with E_dyn and E_leak labeled, MEP marked clearly.
- Cite: Calhoun, Wang, Chandrakasan (2005), IEEE JSSC.
- Anticipated question: *"Does the MEP stay fixed?"* Answer: No — it shifts dynamically with temperature, process corner, and aging. That's exactly why we need ADVC (teaser for Slide 7).

---

### Slide 5 — Sizing for Energy: "Small is Beautiful" (Presenter 2, 15:00–18:00)
**Required content:**
- In standard design: upsize gates for speed.
- In sub-threshold: upsizing is always a losing trade because:
  - Wider transistors → higher capacitance → higher E_dyn
  - Wider transistors → larger leakage footprint → higher E_leak
  - Speed improvement in sub-VT is sub-linear and cannot offset both costs
- Calhoun's rule: W_min, L_min is optimal for sub-threshold.
- Important nanoscale caveat: In FinFET and GAAFET nodes (5nm, 3nm, 14Å), width is quantized (you add fins, not continuous width). The W_min rule from 180nm may not hold — this is an open research gap relevant to our Phase 04 innovation.
- Cite: Calhoun et al. (2005).

---

### Slide 6 — The Variability Nightmare + SRAM (Presenter 2, 18:00–23:00)
**Required content — TWO sub-sections on this slide:**

**Part A: Logic Variability**
- Because I_sub is exponential in V_th, a 10mV local V_th shift (from Random Dopant Fluctuation or fin-edge roughness) causes a 2–3× change in path delay.
- At the "Slow" process corner, a chip designed for the MEP at the "Typical" corner may fail entirely.
- Traditional solution (fixed worst-case margin) pushes V_DD so far above the MEP that the energy benefit is erased.

**Part B: SRAM Failure (this is a critical point — do not skip)**
- Connect to what the class learned about low-power memory: Standard 6T SRAM cells fail in sub-threshold *before* the logic does.
- Two failure mechanisms:
  1. Read Disturb: the bitline voltage rise during a read flips the storage node when SNM (Static Noise Margin) collapses near zero.
  2. Write Margin: reliable writes fail below ~400mV.
- Key conclusion: in a real system, SRAM stability — not logic delay — often sets the lower bound on V_DD.
- Killer fact: "In sub-threshold, the memory fails before the logic does. That's the real floor."
- Anticipated question: *"Can't we just design with worst-case margins?"* Answer: Yes, but that destroys the efficiency benefit. You need adaptive real-time control — which is exactly what we cover next.

---

### Slide 7 — ADVC: The Silicon Solution (Presenter 2, 23:00–26:00)
**Required content:**
- Adaptive Dynamic Voltage Control (ADVC) from Zangi et al. (2018), 40nm TSMC.
- The core idea: design for the typical corner, adapt in real-time instead of guarding for the worst case.
- Three components to explain:
  1. On-die sensors: continuously monitor temperature, process corner, and aging.
  2. First-Fail (FFail) Monitor: a programmable delay chain tuned to fail *just before* the real critical path fails. It is a hardware tripwire — it fires before any functional error occurs, giving the PMIC time to boost V_DD proactively.
  3. Closed-loop control: V_DD adjusts dynamically between 0.45V and 1.1V to track the MEP across all PVT conditions.
- Silicon results:
  - 18 μA/MHz at 0.45V (40nm)
  - 3–5× more efficient than ARM Cortex-M4F
- Analogy: "ADVC is cruise control for energy efficiency — it holds you at the MEP regardless of the terrain."
- Cite: Zangi et al. (2018), JLPEA.

**Handoff line to Presenter 3:** Something like: "So far we've covered the theory and the core silicon solution. [Presenter 3] will show us where this is going — edge AI and today's most advanced chips."

---

### Slide 8 — Edge AI: The LAXOR Accelerator (Presenter 3, 26:00–29:00)
**Required content:**
- Connect to what the class knows about Power-Aware ML Hardware (a neighboring topic in the course).
- Binary Neural Networks (BNNs) reduce MAC to XNOR + popcount — already very efficient.
- The problem: even BNN accelerators use energy-heavy flip-flops, which are especially costly in sub-VT (high setup/hold sensitivity, switching on every clock edge).
- LAXOR's solution: Latch-XOR Logic Array
  - Weights stored in latches (~60% less switching energy than flip-flops)
  - XOR gate fused directly to latch output: XNOR computation happens during the latch clock transition — no separate logic stage
  - Optimized adder tree: 1.37× energy-delay improvement
- Results (28nm): 2315 TOPS/W, CIFAR-10 accuracy 85.25%, bit-accurate and deterministic.
- Why it beats analog Compute-in-Memory: no analog noise at the decision boundary — reliability is preserved.
- Cite: LAXOR, ISLPED Best Paper 2023.
- Killer fact: "2315 TOPS/W — by eliminating the flip-flop, the single most energy-wasteful element in sub-VT digital design."

---

### Slide 9 — Nanoscale SOTA: IBM 5nm AI SoC (Presenter 3, 29:00–32:00)
**Required content:**
- Connect to the course's Device Level topic: Nano-Sheet FETs (GAAFET) replace FinFETs at 5nm — gate wraps around all four sides of the channel for better drive current control at low voltages.
- The key shift: at 5nm density, the chip hits its thermal wall before its frequency limit. NTC is mandatory.
- Multi-voltage domain strategy: compute-heavy blocks at 0.55V (NTC), timing-critical paths at 0.75V.
- Dual-loop peak current regulation:
  1. Feed-Forward (SW): Compiler predicts high-current bursts and throttles frequency proactively before they form.
  2. Hardware Feedback: Microsecond-granularity on-die current sensors cap any unforeseen peaks.
  - Together, these keep the chip within the 75W PCIe power limit at NTC voltages.
- Result: 41% improvement in BERT-Large inference throughput through active NTC management.
- The open gap: no equivalent of Zangi's ADVC has been built natively for GAAFET 5nm. This is the gap our Phase 04 innovation will address.
- Cite: IBM Research, ISSCC 2024.

---

### Slide 10 — Summary & Innovation Bridge (Presenter 3, 32:00–33:00)
**Required content:**
- A single timeline visual: Calhoun 2005 → Dreslinski 2010 → Zangi 2018 → LAXOR 2023 → IBM 2024 → "Phase 04: Our Proposal"
- Three pillars in one line each:
  - MEP is the goal
  - Variation is the enemy
  - Adaptation is the tool
- One sentence teasing Phase 04: "The next step is bringing Zangi's closed-loop ADVC into the 5nm GAAFET era — and making it AI-workload aware."
- Closing line: Something memorable that invites the Kahoot as a natural next step, not an awkward pivot.

---

## A4. Kahoot Section (Presenter 3 hosts, 33:00–45:00)

Generate exactly 6 questions. Format for each:
- Question stem (one sentence, clear and unambiguous)
- 4 answer choices (A, B, C, D) — one correct, three plausible distractors
- Mark the correct answer clearly
- A 2-sentence "after reveal" explanation for the presenter to read aloud

The 6 questions must cover these concepts, one each:
1. The MEP voltage range (Calhoun 2005)
2. Calhoun's W_min sizing rule
3. The 10×/10× rule (Dreslinski 2010)
4. The role of the FFail monitor (Zangi 2018)
5. Which component fails first in sub-VT (SRAM vs. logic)
6. IBM's dual-loop regulation result (IBM 2024)

Difficulty: medium. These should be answerable by someone who paid attention, not trivial for someone who didn't.

---

## A5. Per-Slide Quality Standards

For every slide, provide:
1. **Slide title** (short, punchy — max 6 words)
2. **Visual description** (specific enough for a designer to execute — what type of graphic, what data it shows, what labels are needed)
3. **Bullet points** (3–5 max on screen — not full sentences, just anchors)
4. **Full speaker notes** (complete paragraphs the presenter can read to prepare — NOT to read aloud verbatim, but to internalize)
5. **Killer fact** (one sentence the presenter can deliver for emphasis)
6. **Anticipated Q&A** (one student question + the correct answer)

Do not write slide bullets as full paragraphs. Keep on-screen text minimal — the speaker notes carry the detail.

---

## A6. Tone & Style

- **Peer-to-peer, not top-down.** We are students teaching students. Confident, not condescending. Enthusiastic about the topic, not performatively academic.
- **Visual-heavy, text-light.** Every slide needs a strong visual anchor. Text on slides is for reference, not reading.
- **Humor is welcome** in the framing (the course explicitly encourages this — Prof. Fish used comics and cartoons in Lecture 1). One light moment per section is appropriate.
- **Technically rigorous.** We are in an engineering course. Equations and numbers must be correct. Do not round or approximate in ways that change meaning.
- **Build the story.** Each slide should feel like the next natural question: Slide 3 asks "where?" Slide 4 answers "how low?" Slide 5 asks "how to size?" Slide 6 asks "what can go wrong?" Slides 7–9 answer "what does industry do?" Slide 10 says "what's next?"

---

---

# TASK B: SHORT VIDEO SUMMARY

## B1. Context & Constraints

- **Duration:** Exactly 3 minutes (±10 seconds)
- **Tool:** This script will be used to generate an AI-narrated video (e.g., via Sora, HeyGen, or similar)
- **Audience:** The same class — engineering students who have now heard the presentation and want a concise, re-watchable summary
- **Grade weight:** 10% of total course grade
- **Tone:** More dynamic and cinematic than the presentation. Shorter sentences. More energy. Think "explainer video" not "lecture recording."
- **Structure:** 4 scenes, each with its own visual direction and audio script

---

## B2. Scene-by-Scene Requirements

### Scene 1 — The Hook (0:00–0:30, 30 seconds)
**Audio goal:** Grab attention in the first 5 seconds. Acknowledge what the class already knows, then pull the rug: "But what if the circuit itself is the solution?"

Required elements:
- Reference that clock gating and power gating are reactive — they shut things down
- Introduce sub-threshold as the *proactive* alternative
- End with a question that creates tension for the rest of the video: "What's the physical limit of energy efficiency on silicon?"

Visual: A zoomed-in chip, then a "battery low" icon transitioning to a glowing transistor diagram. Fast cut pace.

---

### Scene 2 — The Physics & The MEP (0:30–1:15, 45 seconds)
**Audio goal:** Explain MEP clearly enough that a student who missed the lecture would understand it.

Required elements:
- Sub-threshold relies on diffusion current, not drift — it's exponential
- The MEP: as V_DD drops, E_dyn falls but T_clk rises exponentially, so leakage energy eventually overtakes dynamic savings
- The sweet spot is ~250–400mV — below that you waste energy
- The 10×/10× rule: NTC gives 10× energy improvement at 10× performance cost

Visual: Animated U-shaped energy curve building in real-time. Annotate E_dyn and E_leak lines. Flash "MEP" at the minimum.

---

### Scene 3 — The Solution: ADVC + Silicon Proof (1:15–2:15, 60 seconds)
**Audio goal:** Explain why variation breaks naive sub-VT design, and how ADVC solves it. Then prove it with real numbers.

Required elements:
- In sub-VT, a 10mV variation in V_th causes 2–3× delay spread — this is the variability nightmare
- Static worst-case margins erase the energy benefit entirely
- ADVC (Zangi 2018) tracks the MEP in real time using on-die sensors and First-Fail monitors
- Numbers: 18 μA/MHz at 0.45V, 3–5× better than Cortex-M4F
- Bridge to AI: LAXOR (2023) takes this further — 2315 TOPS/W by replacing flip-flops with Latch-XOR logic
- IBM 5nm (2024): 41% BERT-Large throughput gain via NTC with software-hardware co-regulation

Visual: Block diagram of ADVC lighting up. Cut to a BNN accelerator schematic. Cut to a chip cross-section labeled "5nm GAAFET."

---

### Scene 4 — The Future & Call to Action (2:15–3:00, 45 seconds)
**Audio goal:** Connect everything to where the field is going. End with one memorable line.

Required elements:
- From biosensors to AI SoCs, sub-threshold is now a design requirement, not a curiosity
- The open frontier: bringing ADVC into the 14Å GAAFET era — no one has done this yet
- Closing line: something that makes the student feel like they are now at the edge of the field, not just studying history

Visual: Fast montage — medical implant, AI chip, tiny solar harvester, circuit diagram with a question mark labeled "14Å ADVC?" End on a clean title card.

---

## B3. Audio Script Standards

- Write the full audio script word-for-word (not bullet points)
- Sentences should average 12–15 words. Nothing over 25 words.
- Read-aloud timing: aim for ~130 words per minute
- Avoid passive voice
- Every number cited must match the source documents exactly
- At least 3 paper citations must be made explicit by name/year in the narration (e.g., "Calhoun's 2005 model," "Zangi's 2018 silicon prototype," "IBM's 2024 ISSCC result")

---

## B4. Visual Direction Standards

For each scene, provide:
1. A one-sentence description of the opening shot
2. Any animations or transitions (zoom, fade, graph building, etc.)
3. Key text overlays (terms, numbers, equations to flash on screen)
4. Pacing note (calm/fast/dramatic)

---

---

# TASK C: CROSS-CHECK AGAINST COURSE REQUIREMENTS

Before finalizing either output, verify the following checklist:

**Technical coverage (from the course topic landscape):**
- [ ] Motivation and basics of low-power design are established before diving into sub-VT
- [ ] Dynamic, static (leakage), and short-circuit power components are mentioned or assumed as prior knowledge
- [ ] Nanometer transistor models referenced (FinFET / GAAFET / 14Å)
- [ ] Sub-threshold AND near-threshold are both covered (not just one)
- [ ] Memory (SRAM) failure in sub-VT is addressed
- [ ] Architecture-level solution (massive parallelism) is mentioned (Dreslinski)
- [ ] Logic-level technique (latch vs. flip-flop) is covered (LAXOR)
- [ ] Novel paradigm / Power-Aware ML Hardware is connected (LAXOR, IBM)
- [ ] At least one reference to a 14Å / 5nm process node (IBM 2024)

**Presentation logistics:**
- [ ] Exactly 10 slides + Kahoot
- [ ] 3 presenter assignments are explicit on every slide
- [ ] Handoff lines between presenters are scripted
- [ ] Total time ≤ 45 minutes (33 min slides + 12 min Kahoot)
- [ ] All 5 papers are cited at least once by name and year

**Video logistics:**
- [ ] Total narration is 3 minutes ± 10 seconds (verify by word count ÷ 130 wpm)
- [ ] Four scenes with distinct visuals and audio
- [ ] At least 3 papers cited explicitly in narration

**Quality:**
- [ ] Every equation matches source documents exactly
- [ ] Every number (18 μA/MHz, 2315 TOPS/W, 41%, 300mV, 10×, etc.) matches sources
- [ ] Tone is peer-to-peer, engaging, and technically rigorous throughout

---

## Final Instruction to NotebookLM

You have all five source documents loaded. Use them as your only technical source — do not hallucinate numbers or equations. If a source says the MEP is ~300mV in 180nm, write exactly that. If a source says 18 μA/MHz, write exactly that.

Your job is to produce:
1. A complete, finalized, 10-slide presentation script (slide content + visual directions + full speaker notes + Kahoot questions) ready to hand to three students who will present in 45 minutes.
2. A complete, finalized, 3-minute video script with word-for-word narration and scene-by-scene visual direction ready to feed into an AI video generation tool.

Both outputs should feel like they came from students who deeply understand the material and are excited to share it — not from a textbook or a template. The bar is: a classmate who attended this lecture and watched this video should be able to answer every Kahoot question correctly and feel like they understand sub-threshold computing at an intuitive level.

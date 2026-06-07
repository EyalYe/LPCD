# Speaker 4 — Script (Nadav)

**Topic:** RDF, Applications & Conclusion (LAXOR)
**Time:** 30:00 – 39:00 (9 min) — closes the talk.
**Companion slide file:** [speaker_4_applications_and_conclusion(Nadav).md](speaker_4_applications_and_conclusion(Nadav).md)
**Format:** Verbatim narration — one block per slide. Read as written; timings are targets, not hard cuts.

---

## Slide 1 — Recap & The Robustness Fork (30:00 – 31:30)

> "Thanks, Eli. Let me recap the arc so far: we covered the physics and the Minimum Energy Point with Omri, then adaptive tracking in real silicon with Eli. Now the question is — what do we actually run on these chips? It all comes back to the variability root cause we keep meeting: Random Dopant Fluctuation, plus line-edge roughness, which turns a *linear* spread in threshold voltage into an *exponential* spread in current and delay at low voltage. There are two philosophies for living with that spread. One: tolerate the error — that's approximate computing, the second axis Eyal introduced, where you relax exactness to buy energy. Two: refuse the error — stay bit-accurate, and win energy instead by cutting data movement and switching activity. That second path is the one our final paper, LAXOR, takes."

---

## Slide 2 — The Workload: Binary Neural Networks at the Edge (31:30 – 33:00)

> "The workload is edge AI — machine-learning inference on a coin cell or harvested power, exactly the energy-first regime this whole talk targets. The key trick is Binary Neural Networks. In a BNN, weights and activations are just plus or minus one — a single bit. That means the expensive multiply-accumulate collapses into an XNOR followed by a popcount: XNOR asks 'do the weight and the input agree?', and popcount just counts how many agreements there were. That's the entire neuron. Now, there were two prior approaches, each with a flaw. Analog, or mixed-signal compute-in-memory, is very efficient — but it's inaccurate, because it's sensitive to noise, temperature, and process variation, which is exactly the RDF problem biting again. All-digital is accurate — but it keeps the weight storage and the XOR gate separate, so it constantly moves data back and forth, and that movement wastes energy. LAXOR's whole pitch is to get both: accurate *and* efficient."

---

## Slide 3 — LAXOR's Core Idea: Fuse Storage + Compute (33:00 – 34:45)

> "Here's the headline contribution: a custom ten-transistor Latch-XOR cell. A cross-coupled latch holds the weight in place, and a small two-transistor path plus an inverter computes weight-XOR-input *right where the weight lives* — in situ, with no data movement. The input line is even time-multiplexed for both weight-loading and data, which saves area. Compared to the decoupled approach — a standard-cell latch plus a separate XOR — this gives 4.2× lower energy per operation, 0.785 femtojoules versus 3.30, and 5× smaller area. And it's robust at low voltage: a ten-thousand-point Monte-Carlo simulation at three sigma shows it fully functional at 0.5 volts and 200 megahertz using standard-threshold devices — while high-threshold devices fail at that same corner. That ties straight back to our low-voltage theme."

---

## Slide 4 — Popcount, Architecture & the Simulator (34:45 – 36:15)

> "After the XNOR, you need to add up the agreements — that's the popcount. LAXOR uses a PCL popcount, a hybrid of a parallel counter and carry-look-ahead, which gives 1.45 to 6.5× better energy-delay and lower power than binary-adder-tree or look-up-table approaches. The architecture scales this out: four clusters of 64 processing elements each, where every PE holds 1024 Latch-XOR cells plus popcount and bias, with flexible dataflow for different kernel sizes. And they released an open-source, cycle-accurate Python simulator for hardware-software co-design — importantly, it models leakage energy, which, as everyone in this talk has said, is what kills you at low voltage. It runs about eight million times faster than Spectre."

---

## Slide 5 — Results & Why It Validates the Whole Talk (36:15 – 37:45)

> "The results: 2315 TOPS per watt in 28-nanometer — 3.4× better than the best synthesized-digital BNN accelerator, and up to 37.8× better than others. On CIFAR-10 it hits 85.25% accuracy at 3.82 microjoules per inference, which is 1.8× lower energy than the state-of-the-art digital baseline. And crucially, it's bit-accurate — there's no accuracy loss from analog non-ideality, so the RDF and PVT robustness problem is sidestepped entirely, not just tolerated. This is the recurring theme of the whole talk, proven on a 2023 workload: minimizing data movement and switching activity beats raw device speed for energy. The honest caveat is that BNNs are a constrained, 1-bit model class — LAXOR's win is biggest exactly where the workload tolerates binarization. Like everything in this field, it's a co-design story."

---

## Slide 6 — Conclusion: The Arc of the Field (37:45 – 39:00)

> "So let me close the loop. We promised a timeline, and here it is: the theory of the optimum in 2005, the near-threshold sweet spot in 2010, a complementary efficiency axis in 2015, production silicon fighting variability in 2018, and a modern edge-AI use case in 2023. Three takeaways. First: energy is quadratic in supply voltage — it's the most powerful knob — but there's a Minimum Energy Point where leakage stops the savings. Second: variability, rooted in the exponential current–voltage relationship and RDF, is the enemy — and we beat it either with sensors and adaptive voltage control in real silicon, or by sidestepping it with robust, bit-accurate design. Third: for these workloads, energy is not the same as speed — cutting switching activity and data movement is what wins, from minimum sizing, to the fused Latch-XOR, to pruning. Eyal opened with a question: what if the most efficient way to compute is to run the transistor while it's technically off? From 2005 theory to a 2023 edge-AI chip, the answer hasn't changed — in extreme low-power design, the cheapest operation is the one you never had to move. Thank you — questions?"

# 02 Presentation and Video (30%)

## Objective
Teach the class about sub-threshold design and provide a high-level video summary.
4 speakers, each fully owns their section — slides, speaker notes, and Kahoot questions.

## Deliverables
- **Class Presentation (20%)**: 4 separate speaker files + shared video script
- **Kahoot (in-class quiz)**: [kahoot.md](kahoot.md) — all 6 questions, hosted by each speaker after their section
- **Short Video Summary (10%)**: [Video Script](video_script.md) — 4 sections, one per speaker

---

## Speaker Assignment & Timing

| Speaker | Topic | Slides | Slides Time | Kahoot Qs | Total |
|---------|-------|--------|-------------|-----------|-------|
| [Eyal](speaker_1_intro_and_context(Eyal).md) | Introduction & Context | 1–6 | 0:00–10:00 (10 min) | Q3 | ~13 min |
| [Omri](speaker_2_mep_and_sizing(Omri).md) | MEP Theory & Sizing | 7–10 | 10:00–18:00 (8 min) | Q1, Q2 | ~11 min |
| [Eli](speaker_3_variability_and_advc(Eli).md) | Variability & ADVC | 11–14 | 18:00–26:00 (8 min) | Q4, Q5 | ~11 min |
| [Nadav](speaker_4_applications_and_conclusion(Nadav).md) | Applications & Conclusion | 15–18 | 26:00–33:00 (7 min) | Q6 | ~10 min |
| **TOTAL** | | **18 slides** | **33 min** | **6 Qs** | **45 min** |

---

## Kahoot Distribution (each speaker hosts immediately after their section)

| Question | Topic | Hosted By |
|----------|-------|-----------|
| Q1 — MEP voltage range (Calhoun 2005) | MEP Theory | Speaker 2 |
| Q2 — Sizing rules ($W_{min}$) | Sizing | Speaker 2 |
| Q3 — NTC 10×/10× rule (Dreslinski 2010) | Operating Regions | Speaker 1 |
| Q4 — FFail monitor role (Zangi 2018) | ADVC | Speaker 3 |
| Q5 — Which component fails first in sub-VT | Variability & SRAM | Speaker 3 |
| Q6 — IBM 5nm BERT-Large technique | Industry Applications | Speaker 4 |

---

## Full Presentation Timing

| Section | Start | End | Duration |
|---------|-------|-----|----------|
| Slide 1: Title | 0:00 | 1:00 | 1 min |
| Slide 2: End of the Free Lunch (Dennard) | 1:00 | 3:00 | 2 min |
| Slide 3: Two Problems, One Solution | 3:00 | 5:00 | 2 min |
| Slide 4: MOSFET Physics Below $V_{th}$ | 5:00 | 7:00 | 2 min |
| Slide 5: The Three Operating Regimes | 7:00 | 8:30 | 1.5 min |
| Slide 6: The NTC Sweet Spot (Dreslinski) | 8:30 | 10:00 | 1.5 min |
| **→ Omri takes over** | | | |
| Slide 7: Dynamic Energy & The Quadratic Law | 10:00 | 12:00 | 2 min |
| Slide 8: Leakage Energy + The MEP | 12:00 | 14:30 | 2.5 min |
| Slide 9: The MEP is Not Fixed | 14:30 | 16:00 | 1.5 min |
| Slide 10: Sizing for Minimum Energy | 16:00 | 18:00 | 2 min |
| **→ Eli takes over** | | | |
| Slide 11: RDF & The Exponential Amplifier | 18:00 | 20:30 | 2.5 min |
| Slide 12: SRAM — The Weakest Link | 20:30 | 22:30 | 2 min |
| Slide 13: ADVC Architecture (Zangi 2018) | 22:30 | 24:30 | 2 min |
| Slide 14: ADVC Silicon Results | 24:30 | 26:00 | 1.5 min |
| **→ Nadav takes over** | | | |
| Slide 15: Why BNNs at the Edge? | 26:00 | 27:30 | 1.5 min |
| Slide 16: LAXOR Architecture & Results | 27:30 | 29:30 | 2 min |
| Slide 17: IBM 5nm — The Production Frontier | 29:30 | 31:30 | 2 min |
| Slide 18: The Research Roadmap & Phase 04 | 31:30 | 33:00 | 1.5 min |
| **→ Kahoot (distributed after each section)** | | | |
| 6 questions × 2 min | 33:00 | 45:00 | 12 min |
| **TOTAL** | | | **45 min** |

---

## AI Tools
- [x] Gemini for initial slide content generation
- [x] Gemini for video script drafting
- [x] Claude (Claude Code) for full refactor — restructured into 18 slides with proper timing, deepened research content, and rewrote all speaker notes from scratch
- [ ] NotebookLM for audio/script finalization

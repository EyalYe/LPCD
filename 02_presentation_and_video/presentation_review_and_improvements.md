# Presentation Improvement Instructions

# Extreme Low Voltage Computing

## Objective

Improve the current presentation into a clear, engaging, and student-friendly academic presentation.

Target duration:

* 20–25 minutes total
* 3 presenters
* Approximately 18–21 slides

The presentation should feel like a coherent story rather than a collection of paper summaries.

---

# Visual Style Requirements

Keep the existing design language:

* Light gray background
* Teal accents
* Soft green highlights
* Modern conference style
* AI-inspired visuals
* Large diagrams and illustrations
* Minimal text per slide

Avoid:

* IEEE paper appearance
* Dense text blocks
* Overloaded tables
* Dark themes

---

# Presentation Structure

## Presenter 1

# Introduction & Motivation

Target Time:
7–8 minutes

Target Slides:
6–7

---

### Slide 1

Title Slide

Operating Circuits at Extreme Low Voltages

Subtitle:

Foundations, Adaptive Control, and Industry Applications

---

### Slide 2

Why Energy Became the Main Constraint

Topics:

* IoT
* Edge AI
* Always-On Systems
* Thermal limitations

Main Message:

Energy has become the dominant design constraint.

---

### Slide 3

Why Traditional Power Saving Is Not Enough

Topics:

* Clock Gating
* Power Gating
* DVFS

Main Message:

Traditional techniques reduce power but do not achieve minimum energy operation.

---

### Slide 4

Operating Regions Overview

Compare:

* Super-Threshold
* Near-Threshold
* Sub-Threshold

Use a visual current-voltage diagram.

---

### Slide 5

Near-Threshold Computing

Topics:

* Sweet spot
* Energy-performance tradeoff

Main Message:

Near-threshold provides most of the energy benefits with acceptable performance loss.

---

### Slide 6

Sub-Threshold Computing

Topics:

* Diffusion current
* Exponential behavior
* Extreme energy efficiency

Main Message:

Sub-threshold enables ultra-low-power operation but introduces severe variability challenges.

---

### Slide 7

Key Takeaways & Transition

Transition:

How do we determine the optimal operating voltage?

---

# Presenter 2

# Minimum Energy Point & Adaptive Control

Target Time:
7–8 minutes

Target Slides:
6–7

---

### Slide 8

Dynamic Energy

Introduce:

E_dyn ∝ C × V²

Main Message:

Voltage scaling dramatically reduces switching energy.

---

### Slide 9

Leakage Energy

Topics:

* Delay increase
* Leakage accumulation

Main Message:

Lower voltage is not always better.

---

### Slide 10

Minimum Energy Point (MEP)

Show:

* Dynamic energy curve
* Leakage energy curve
* Total energy curve

Highlight:

Optimal operating voltage.

---

### Slide 11

Sizing for Minimum Energy

Based on:

Calhoun et al.

Topics:

* Minimum-sized devices
* Capacitance reduction
* Leakage reduction

---

### Slide 12

Variability & SRAM Challenges

Topics:

* Process variation
* Temperature variation
* Aging
* SRAM failures

Main Message:

Theoretical optimum is difficult to maintain in real silicon.

---

### Slide 13

Adaptive Dynamic Voltage Control (ADVC)

Topics:

* Sensors
* Closed-loop control
* FFail monitoring

Main Message:

Adaptive control keeps the system near the MEP.

---

### Slide 14

Key Takeaways & Transition

Transition:

What becomes possible when we operate reliably at extremely low voltages?

---

# Presenter 3

# Applications & Future Directions

Target Time:
7–8 minutes

Target Slides:
6–7

---

### Slide 15

Industry Applications Overview

Show:

* Edge AI
* Biomedical Electronics
* Batteryless Systems
* Future AI Hardware

Main Message:

Extreme low-voltage computing enables entirely new application domains.

---

### Slide 16

Edge AI & LAXOR

Topics:

* Binary Neural Networks
* Latch-XOR Logic
* Compute-in-Memory concepts

Main Message:

Reducing data movement is as important as reducing voltage.

---

### Slide 17

Batteryless & Biomedical Systems

Topics:

* Energy harvesting
* Wearables
* Implantable devices

Use the low-energy inference example.

Main Message:

Energy efficiency enables products that were previously impossible.

---

### Slide 18

The 3nm / 14Å Frontier

Topics:

* Aging
* Variability
* Power density
* Thermal hotspots

Use IBM 5nm AI SoC as a case study.

Main Message:

Future nanoscale systems require both energy efficiency and adaptive control.

---

### Slide 19

Future Adaptive AI Systems

Topics:

* Predictive power management
* Hardware-software co-optimization
* Adaptive computing

Main Message:

Future AI chips will continuously adapt to changing conditions.

---

### Slide 20

The Paradigm Shift

Visual:

Performance-First

↓

Energy-First

Main Message:

The primary objective of computing is changing.

---

### Slide 21

Final Takeaway

Large Statement:

"Efficiency is the New Performance"

Supporting Message:

Future computing systems will be defined not by how fast they operate, but by how intelligently they use energy.

---

# Content Guidelines

For every slide:

Include:

* Key takeaway
* Suggested visual
* Speaker notes

Focus on:

* Storytelling
* Student understanding
* Clear transitions

Do not create a paper-by-paper review.

Create a single coherent narrative that connects:

Motivation
→ Theory
→ Adaptive Control
→ Real Applications
→ Future Directions

The audience should always understand:

1. What is the problem?
2. Why does it matter?
3. What is the solution?
4. What does it enable?

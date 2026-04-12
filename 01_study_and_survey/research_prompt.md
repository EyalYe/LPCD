# Deep Research Prompt for Paper Gathering

*Copy and paste the following prompt into an advanced AI research tool (e.g., Gemini Advanced, Perplexity, Elicit, or Consensus) to gather the best state-of-the-art papers for our literature survey.*

---

**System Role:** Act as a Senior VLSI Research Scientist specializing in Low Power Digital Circuit Design and Nanoscale Technologies. 

**Task:** I am conducting a deep literature survey for a university course project on **"Operating circuits at extreme low voltages for maximum energy efficiency"**, specifically focusing on **Near-Threshold and Sub-Threshold Computing**. I need you to find, analyze, and summarize the top 10 most influential and state-of-the-art academic papers on this topic.

**Specific Search Criteria & Focus Areas:**
1. **Foundations & MEP:** Papers that clearly define and model the Minimum Energy Point (MEP) where leakage overhead and dynamic power savings intersect.
2. **Extreme Low Voltage (Sub-$V_{th}$):** Focus heavily on circuits operating where the supply voltage ($V_{DD}$) is strictly lower than the threshold voltage ($V_{th}$).
3. **Current Challenges:** Research addressing the main bottlenecks of sub-threshold design: performance variability, degraded noise margins, speed degradation, and severe sensitivity to PVT (Process, Voltage, Temperature) variations.
4. **Modern Nodes:** Prioritize recent papers (last 5-10 years) that discuss these techniques in modern nanoscale processes (e.g., 22nm down to 14Å), though you MUST include 1-2 foundational/seminal papers (even if older) that defined the field.
5. **Novel Solutions:** Highlight papers that propose architectural or circuit-level innovations to overcome these challenges, as my ultimate goal is to propose my own novel method.

**Required Output Format:**
Please present the 10 papers in a structured markdown format. For each paper, provide:

*   **Title:** [Full paper title]
*   **Authors & Year:** [Primary authors, Publication Year]
*   **Venue/Journal:** [e.g., IEEE JSSC, ISLPED, ISSCC, TCAS]
*   **Link/DOI:** [URL or DOI for easy access]
*   **Core Physics/Concept:** [1-2 sentences explaining the underlying sub-threshold concept discussed]
*   **Key Contribution:** [What novel method or finding did this paper introduce?]
*   **Relevance to Project:** [Why is this critical for understanding maximum energy efficiency or inspiring a new novel proposal?]

**Constraints:** 
- Do not include papers focused primarily on analog sub-threshold design; focus on **Digital VLSI**.
- Exclude papers that only discuss standard Dynamic Voltage and Frequency Scaling (DVFS) without crossing into the near/sub-threshold regime.

---
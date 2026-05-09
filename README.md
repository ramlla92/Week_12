# Week 12: Knowledge Gap Formulation & Technical Deep Dives

This repository contains the deliverables for the Week 12 “Knowledge Gap Formulation” challenge in the 10 Academy AI Program.

The goal of this week was to move beyond simply building AI systems and instead investigate the underlying scientific, engineering, and evaluation principles that govern how modern LLM systems behave in production.

Across four days of paired technical research, the work focused on:
- inference-time mechanics,
- tool-use internals,
- post-training behavior,
- and statistical evaluation reliability.

Each research topic was grounded directly in real implementation challenges encountered while building:
- the Tenacious Conversion Engine,
- and the Tenacious-Bench evaluation framework.

---

# Repository Structure

The repository is organized by daily research focus.

## `pair_Day_1/`
### Inference-Time Mechanics
- Prefill vs Decode Latency
- Attention competition
- Instruction drift
- Long-context behavior

---

## `pair_Day_2/`
### Agent & Tool-Use Internals
- Function calling
- Constrained decoding
- Structured generation
- Runtime orchestration

---

## `pair_Day_3/`
### Post-Training Mechanics
- ORPO preference optimization
- Shortcut learning
- Explanation-pattern memorization
- Hard-negative training

---

## `pair_Day_4/`
### Evaluation & Statistics
- Paired bootstrap resampling
- Confidence intervals
- Benchmark variance
- Evaluation reliability

---

# Deliverables Included in Each Day Folder

Each research folder contains the complete paired research loop:

- `question.md` — Final sharpened research question  
- `morning_call_summary.md` — Question refinement discussion summary  
- `explainer.md` — Technical explainer/blog post written for a peer  
- `thread.md` — Public social thread for dissemination  
- `evening_call_summary.md` — Revision and feedback summary  
- `signoff.md` — Peer gap-closure assessment  
- `grounding_commit.md` — Portfolio grounding update connected to Weeks 10/11 artifacts  
- `sources.md` — Papers, tools, and canonical references used during research  

---

# Final Synthesis Artifacts

- `synthesis.md`  
  Comprehensive synthesis of the technical insights and engineering patterns discovered throughout the week.

- `canonical_list.md`  
  Curated list of the core concepts, mechanisms, papers, and engineering patterns explored during the research process.

- `portfolio_update.md`  
  Portfolio-oriented summary connecting the research to the SDR Agent and Tenacious-Bench systems.

---

# Public Artifacts

| Day | Topic | Blog Post | Thread |
|---|---|---|---|
| 1 | Prefill vs Decode Latency | [Medium Post](https://medium.com/@akmelremu/why-your-llm-feels-slow-understanding-prefill-vs-decode-3995758dd253) | [Thread Link](https://x.com/akmel3784/status/2051739738533412867?s=20) |
| 2 | Constrained Decoding | [Medium Post](https://medium.com/@akmelremu/function-calling-in-ai-agents-why-reliability-is-more-about-systems-than-intelligence-c1d3176d64f7) | [Thread Link](https://x.com/akmel3784/status/2052103493402472877?s=20) |
| 3 | Shortcut Learning | [Medium Post](https://medium.com/@akmelremu/when-ai-learns-the-explanation-instead-of-the-reasoning-f2a3fa2c4e0e) | [Thread Link](https://x.com/akmel3784/status/2052385563395736034?s=20) |
| 4 | Bootstrap Resampling & Benchmark Statistics | [Medium Post](#) | [Thread Link](https://x.com/akmel3784/status/2052833560688480499?s=20) |

---

# Core Themes Explored

Throughout the week, the research repeatedly converged on several central themes:

- Optimization pressure shapes model behavior
- Reliability often comes from systems engineering constraints
- Preference optimization can encourage shortcut learning
- Evaluation systems themselves require auditing
- Statistical assumptions strongly affect benchmark conclusions

---

# Final Reflection

The central lesson from Week 12 is that building reliable AI systems requires much more than stronger models or larger datasets.

Reliable agent systems emerge from carefully aligning:
- prompts,
- decoding behavior,
- preference objectives,
- runtime constraints,
- evaluation metrics,
- and statistical assumptions

with the real behavior the system is intended to produce.

---

*Created by Ramlla Akmel — 10 Academy AI Program*
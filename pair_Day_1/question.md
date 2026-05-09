# Question

In my Tenacious Conversion Engine, I built `agent/outreach_generator.py` to generate highly personalized outbound emails using large enrichment inputs (Crunchbase firmographics, job signals, layoffs data, and dense competitor gap briefs).

While reviewing Langfuse traces, I observed high end-to-end latency. However, my current telemetry only logs total API wait time, treating inference as a single opaque step.

This creates a blind spot. I cannot distinguish whether latency is dominated by:
- Prefill (processing large input context and building attention states), or  
- Decode (sequential token generation of the email)

As a result, I cannot determine whether to optimize context size (prefill) or model speed/output length (decode).

**How do prefill and decode phases contribute differently to end-to-end latency during large-context inference, and what telemetry should I add to `outreach_generator.py` to isolate Time-To-First-Token from token streaming performance?**

# Connection to Week 10/11 Artifact

This gap is grounded in my Week 10/11 implementation of `outreach_generator.py`, where I currently log only total latency per LLM call.

Because this metric collapses fundamentally different phases of inference into a single number, I cannot diagnose performance bottlenecks in my system.

Closing this gap would allow me to:
- Separate Time-To-First-Token (prefill latency)
- Measure token generation rate (decode performance)
- Make targeted improvements to either prompt size or model selection

Without this, my optimization efforts are guesswork rather than mechanism-driven.
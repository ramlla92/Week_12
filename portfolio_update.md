# Portfolio Update — Week 12 Technical Deep Dives

During Week 12, I conducted a series of technical deep dives focused on the internal mechanics of modern LLM systems, grounded directly in failures and observations from my SDR outreach agent and Tenacious-Bench evaluation pipeline.

The research explored four major areas:

- inference mechanics and instruction drift,
- tool-use internals and constrained decoding,
- post-training preference optimization,
- and statistical evaluation reliability.

A major focus was understanding why highly personalized SDR systems still regress toward generic outputs despite strong enrichment signals. Through studying attention dynamics, decode-time competition, and long-context behavior, I learned how instruction salience weakens when prompts become overloaded with competing constraints. This connected directly to failures I observed in my outreach generation pipeline, including perspective drift and loss of semantic richness.

I also explored modern function-calling systems and learned that reliable tool use is often enforced through constrained decoding and schema-level runtime guarantees rather than pure model reasoning. This shifted my understanding of agent reliability from a prompting problem to a systems engineering problem involving orchestration, grammar constraints, and runtime validation.

Another major area was post-training mechanics using ORPO preference optimization. While working with SDR preference datasets, I investigated how the semantic relationship between chosen and rejected outputs shapes learned behavior. I found that “Near-Miss” rejected samples create significantly stronger calibration signals than obviously generic failures because they force the model to learn subtle grounding and personalization boundaries instead of broad stylistic differences.

Finally, I focused heavily on evaluation and statistical rigor. This included:
- auditing LLM-as-a-judge bias,
- understanding fluency and stylistic preference in automated evaluation,
- and learning why paired bootstrap resampling is necessary for shared-task benchmark comparisons.

These investigations changed how I think about AI engineering. I now approach LLM systems less as isolated prompting problems and more as optimization systems shaped by:
- decoding dynamics,
- preference objectives,
- evaluation incentives,
- runtime constraints,
- and statistical assumptions.

Overall, Week 12 significantly strengthened my understanding of:
- inference-time mechanics,
- preference tuning,
- evaluation reliability,
- and the relationship between optimization pressure and model behavior in production AI systems.
# Optimization Targets Shape Model Behavior More Than Raw Capability

## Original Gap

Across Weeks 10 and 11, I repeatedly encountered a confusing pattern in my SDR outreach system:

- highly personalized enrichment data still produced generic emails,
- explicit instructions were ignored during generation,
- ORPO-trained models still collapsed toward repetitive phrasing,
- and evaluation metrics sometimes rewarded outputs that sounded professional even when they were poorly grounded.

At first, I treated these as separate implementation problems:
- prompting problems,
- training problems,
- or evaluation bugs.

Over time, I realized they were all connected by a deeper issue:
> the system was optimizing for the wrong signals.

---

## What I Learned

Large language models do not simply “follow instructions” or “learn reasoning.”

They optimize statistical signals present during:
- training,
- decoding,
- preference optimization,
- and evaluation.

If those signals are misaligned with the intended behavior, the model will often converge toward:
- low-variance safe outputs,
- shortcut patterns,
- stylistic mimicry,
- or persuasive but weakly grounded generations.

The model is not necessarily failing randomly. In many cases, it is successfully optimizing the easiest available objective.

---

## Key Mechanisms

Several mechanisms repeatedly appeared across my experiments:

### 1. Instruction Competition During Decode

During one-shot generation, safety rules, formatting constraints, enrichment context, and personalization signals compete simultaneously in the residual stream.

When too many constraints are packed into one decode pass, the model often selects lower-variance generic tokens that satisfy all constraints safely rather than generating higher-entropy personalized language.

This explained why my SDR outputs became repetitive despite strong enrichment signals.

---

### 2. Attention Salience and Instruction Drift

Long prompts create attention competition.

If personalization context is buried inside large prompts, instruction salience weakens during decode. Hiring-signal language strongly activates company-perspective continuations learned during training, which can override explicit outreach-agent instructions.

This explained why the model drifted into company voice even when the instruction was present.

---

### 3. Preference Optimization Learns Boundaries From Contrasts

In ORPO training, the semantic relationship between chosen and rejected responses becomes part of the learning signal itself.

Generic rejected outputs only teach the model to avoid obviously bad templates.

Near-miss rejected outputs teach finer boundaries:
- grounded vs invented personalization,
- calibrated vs overconfident claims,
- real specificity vs fake specificity.

This changed how I think about preference dataset construction.

---

### 4. Evaluation Systems Can Reward the Wrong Behavior

LLM-as-a-judge evaluation systems can unintentionally reward:
- fluency,
- confidence,
- response length,
- and professional tone

instead of factual grounding.

This means hallucinated SDR emails may receive high scores simply because they sound persuasive.

Evaluation itself therefore requires auditing.

---

### 5. Statistical Confidence Depends on Correct Assumptions

Benchmark statistics are only meaningful if the statistical assumptions match the experimental setup.

Using paired bootstrap correctly matters because benchmark tasks are shared across conditions. Incorrect resampling assumptions can produce misleading confidence intervals and false certainty about model improvements.

---

## Practical Engineering Implications

This changed how I think about building LLM systems.

Good outputs are not only a function of:
- larger models,
- better prompts,
- or more data.

They depend on:
- how objectives are structured,
- how constraints compete during decoding,
- how preference pairs are designed,
- how evaluation metrics are validated,
- and whether statistical conclusions reflect the real deployment setting.

For SDR systems specifically, this means:
- reducing instruction overload,
- separating planning and drafting when necessary,
- using near-miss preference negatives,
- auditing evaluator bias,
- and using statistically valid benchmark methods.

---

## Final Insight

The most important thing I learned is that modern LLM systems are highly sensitive to optimization pressure.

Whatever signal is easiest to optimize —
whether generic safety language, stylistic fluency, or shallow personalization —
will often dominate generation unless the system is explicitly designed to reward the behavior you actually want.

Building reliable AI systems therefore requires not only improving model capability, but carefully aligning:
- prompts,
- training objectives,
- preference datasets,
- evaluation metrics,
- and statistical assumptions

with the real behavior the system is intended to produce.
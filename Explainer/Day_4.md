# Understanding Paired Bootstrap Confidence Intervals in Benchmark Evaluation

## Core Idea

When you compare two models on the same held-out tasks, you are not only measuring:
- “How good is each model?”

You are measuring:
> “How different are the models on the same task distribution?”

This is why paired statistical evaluation matters.

In your Week 11 benchmark, you reported:

```text
Delta A = +16.4
95% CI [12.1, 20.7]
p = 0.003
```

using paired bootstrap resampling.

The important question is:
> What does this confidence interval actually represent, and why is paired bootstrap the correct method?

---

# 1. What Bootstrap Actually Does

Bootstrap is a resampling method used to estimate uncertainty directly from observed data.

The process is:

```text
Observed tasks
        ↓
Repeated resampling with replacement
        ↓
Many simulated evaluations
        ↓
Distribution of metric values
```

This distribution is called:
> the bootstrap distribution.

It approximates:
> “If I repeated this benchmark many times on similar tasks, how much would the metric vary?”

---

# 2. Why Pairing Matters

In your benchmark:
- the baseline model,
- and the trained model

are evaluated on the SAME held-out tasks.

Example:

| Task | Baseline | Trained |
|---|---|---|
| A | 0.45 | 0.62 |
| B | 0.70 | 0.81 |

Some tasks are naturally:
- harder,
- easier,
- noisier,
- or more ambiguous.

Pairing preserves this shared task difficulty.

---

# 3. What Paired Bootstrap Does

Paired bootstrap resamples tasks while keeping:

```text
(Task A baseline, Task A trained)
```

together.

This isolates:
```text
model improvement
```

while controlling for:
```text
task difficulty variance
```

The statistic being estimated is:

```text
trained_score - baseline_score
```

on the same tasks.

---

# 4. What Goes Wrong With Unpaired Bootstrap

Unpaired bootstrap resamples baseline and trained outputs separately.

This breaks task correspondence.

The comparison can accidentally become:

```text
easy trained tasks
vs
hard baseline tasks
```

which inflates variance and distorts the confidence interval.

The benchmark no longer measures:
> performance difference on identical tasks.

---

# 5. What the Confidence Interval Means

Your CI:

```text
[12.1, 20.7]
```

means:

> Across many bootstrap resamples, the estimated improvement usually falls within this range.

It is an uncertainty estimate derived from simulated benchmark reruns.

---

# 6. Bootstrap Iterations

Bootstrap iterations control estimate stability.

| Iterations | Behavior |
|---|---|
| 100 | Noisy |
| 1,000 | Usually sufficient |
| 10,000 | More stable tails |

More iterations:
- reduce Monte Carlo noise,
- improve CI stability,
- but increase compute cost.

For most benchmarks:
```text
1,000–10,000
```

is enough.

---

# 7. Understanding the p-value

Your p-value formula:

```python
sum(differences <= 0) / n_iterations
```

measures:
> how often bootstrap resamples produce zero or negative improvement.

If very few resamples produce:
```text
difference <= 0
```

the improvement appears statistically reliable.

This assumes:
> exchangeability under the null hypothesis,

meaning the observed task differences are representative random samples from the underlying task distribution.

---

# 8. Practical Things You Should Do

## Use Paired Bootstrap for Shared Tasks

If both models are evaluated on the same held-out tasks, preserve pairing during resampling.

---

## Resample Tasks, Not Individual Outputs

The task should usually be the statistical unit.

---

## Report Confidence Intervals

Do not report only:
```text
Pass@1 = 72%
```

without uncertainty estimates.

---

## Use Sufficient Iterations

Recommended:
```text
1,000–10,000
```

for stable benchmark reporting.

---

## Match Statistics to Experimental Design

Use:
- paired methods for paired evaluations,
- unpaired methods only for independent datasets.

Incorrect statistical assumptions can produce misleading benchmark conclusions.

---

# Key Insight

Bootstrap confidence intervals are simulated benchmark reruns created from your observed task distribution.

Paired bootstrap is necessary when the same tasks appear across experimental conditions because it preserves task-level structure and isolates real model differences from task difficulty noise.

Without understanding these assumptions, benchmark metrics can appear more precise than they actually are.

---

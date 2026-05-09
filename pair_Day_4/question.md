# Day 4
# Title

Understanding Paired Bootstrap Confidence Intervals in Benchmark Evaluation

# The Gap

In my Week 11 Tenacious-Bench evaluation, I reported a performance improvement of:

```text
Delta A = +16.4
95% CI [12.1, 20.7]
p = 0.003
```

using paired bootstrap resampling.

Although I can run the bootstrap evaluation code correctly, I realized I do not fully understand what the bootstrap distribution actually represents statistically or why paired bootstrap is the correct method for my benchmark setup.

My held-out evaluation tasks appear in both:
- the baseline condition,
- and the trained model condition.

This raised several questions:
- Why is paired bootstrap appropriate for this setup instead of unpaired bootstrap?
- What happens to the confidence interval if unpaired bootstrap is used incorrectly?
- How many bootstrap iterations are actually necessary for reliable estimates?
- Why is the p-value computed from the proportion of bootstrap differences less than or equal to zero?

# The Question

How does paired bootstrap resampling estimate confidence intervals and statistical significance for held-out benchmark evaluations, and why is it more appropriate than unpaired bootstrap when the same tasks appear across multiple experimental conditions?

Additionally:
- how does bootstrap iteration count affect estimate stability,
- and what assumptions make the bootstrap p-value valid?


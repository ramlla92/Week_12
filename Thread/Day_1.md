URL: https://x.com/akmel3784/status/2051739738533412867?s=20

1/ When measuring LLM latency, it’s common to log total response time.
But this hides what’s actually happening during inference.

LLM generation is not a single step — it consists of two distinct phases.

2/ Phase 1: **Prefill**
The model processes the entire input prompt and computes attention across all tokens.

* Depends on input length
* Happens before any output
* Determines Time-To-First-Token (TTFT)

3/ Phase 2: **Decode**
The model generates output tokens one at a time (autoregressive).

* Sequential process
* Depends on output length
* Determines tokens-per-second (streaming speed)

4/ These phases behave differently:

* Prefill → compute-heavy, scales with input size
* Decode → sequential, scales with output length

This is why large prompts and long outputs impact latency in different ways.

5/ If you only measure total latency, you cannot identify the bottleneck.

Instead, log:

* Start time
* First token time (TTFT)
* End time
* Token count

This allows you to separate prefill and decode performance.

6/ Once separated:

* High TTFT → optimize prompt size / context
* Slow streaming → optimize model or output length

Latency is not one problem — it is two different mechanisms that must be measured independently.

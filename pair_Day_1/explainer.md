# Prefill vs Decode: Why Your LLM Latency Is a Black Box (Until You Measure It Properly)

## The Problem

For the Tenacious Conversion Engine, I built `agent/outreach_generator.py` to generate highly personalized outbound emails.

To produce high-quality emails, the system consumes a large amount of enrichment data:
- Crunchbase firmographics  
- Job post velocity  
- Layoffs.fyi signals  
- Dense competitor gap briefs  

This results in extremely large input prompts.

When I inspected my Langfuse traces, I noticed that generation latency was consistently high.

However, my telemetry had a critical flaw: I only logged total API wait time.

In practice, my code looked like this:

```python
start = time.time()
response = llm_call(...)
end = time.time()

total_latency = end - start
```

## Measuring Prefill vs Decode in Practice

To make this distinction actionable, we need to move from conceptual understanding to instrumentation.

The key idea is to split total latency into two measurable components:

- **Time-To-First-Token (TTFT)** → approximates prefill latency  
- **Token generation rate (tokens/sec)** → characterizes decode performance  

This requires moving beyond a single stopwatch measurement and instead capturing multiple timestamps during inference.

In a streaming setup, this can be implemented by:

1. Recording request start time  
2. Capturing the timestamp when the first token is received  
3. Capturing the timestamp of the final token  
4. Counting total output tokens  

From these, we can derive:

- **Prefill latency ≈ TTFT = first_token_time - start_time**  
- **Decode latency = total_time - TTFT**  
- **Tokens per second = output_tokens / decode_latency**

This separation is critical because prefill and decode scale differently.

---

## Why Prefill and Decode Behave Differently

The distinction between prefill and decode arises from how transformers compute attention.

During **prefill**, the model processes the entire input sequence at once. Attention is computed across all tokens, which has quadratic complexity with respect to input length (O(n²)). This is why large prompts like dense competitor gap briefs can significantly increase latency.

Work such as *FlashAttention* shows that optimizing memory access patterns and attention computation is critical for improving this phase. Prefill is therefore:
- **Compute-heavy**
- Sensitive to **input length**
- Optimizable via **prompt compression or attention efficiency**

In contrast, **decode** operates autoregressively:
- Each token is generated one at a time
- Each step depends on previous tokens
- Computation cannot be parallelized across tokens

Even with KV caching (as discussed in inference optimization literature), decode remains:
- **Sequential**
- **Memory bandwidth bound**
- Dependent on **output length**

This fundamental difference explains why:
- Large inputs hurt prefill
- Long outputs hurt decode

---

## Applying This to Large-Context Evaluation

In my Tenacious Conversion Engine, the system processes:
- Large structured enrichment data
- Dense competitor gap briefs

This creates a **large-context inference scenario**, where prefill becomes a dominant factor.

For example:

- Input: 2,000+ tokens (brief + enrichment)
- Output: ~150 tokens (email)

In this case:
- Prefill must compute attention across all 2,000 tokens  
- Decode only generates 150 tokens sequentially  

If latency is high, it is likely that:
- **Prefill dominates TTFT**, not decode

However, without measurement, this is only a hypothesis.

---

## Telemetry Design in `outreach_generator.py`

To eliminate this blind spot, telemetry must be expanded.

Instead of logging only:

```python
total_latency = end_time - start_time
```

We need to capture structured telemetry:
```
start_time = time.time()

first_token_time = None
tokens = 0

for token in stream_llm_response(...):
    if first_token_time is None:
        first_token_time = time.time()
    tokens += 1

end_time = time.time()

ttft = first_token_time - start_time
decode_time = end_time - first_token_time
tokens_per_second = tokens / decode_time
```
This enables the following metrics:

| Metric | Meaning |
| :--- | :--- |
| TTFT | Prefill latency |
| Decode Time | Token generation duration |
| Tokens/sec | Decode efficiency |
| Input Tokens | Prefill scaling factor |
| Output Tokens | Decode scaling factor |

## Additional Factors That Influence Latency

Beyond prefill vs decode, several system-level factors must be considered:

1. Context Length
Directly increases prefill cost (attention complexity)
Large competitor briefs are a primary driver
2. Model Size
Larger models increase both prefill and decode latency
Decode impact is more noticeable in streaming scenarios
3. KV Cache Usage
Reduces repeated computation during decode
Does not eliminate sequential dependency
4. Hardware Constraints
Prefill is often compute-bound (GPU utilization)
Decode is often memory-bandwidth bound
5. Network and API Overhead
Adds constant latency but does not scale with tokens
Should be separated from model latency when possible


Once prefill is understood to be the problem the following measures could be taken to mitigate it 
 
If TTFT is high, the bottleneck lies in the **prefill phase**.

### What This Means

Prefill corresponds to the model processing the entire input context and computing attention across all tokens.

In my system, this includes:
- Crunchbase firmographics  
- Job post velocity signals  
- Layoffs.fyi data  
- Dense competitor gap briefs  

Because transformer attention scales with input length, large prompts dramatically increase prefill latency. Every additional token increases the amount of computation required to build attention states.

This means that in large-context systems like the Tenacious Conversion Engine, prefill can dominate total latency.

---

## How to Improve Prefill Performance

Improving prefill performance is fundamentally about **reducing or restructuring input tokens**.

### 1. Reduce Input Size (Highest Impact)

The most effective way to reduce prefill latency is to **send fewer tokens to the model**.

In my system, this means:
- Replacing raw competitor briefs with concise summaries  
- Removing redundant enrichment fields  
- Converting verbose text into structured key-value inputs  

For example:

Instead of passing:
> A full multi-paragraph competitor analysis

Pass:
> Top 3 competitor gaps + 1–2 supporting signals

This reduces the number of tokens the model must process, directly lowering prefill cost.

---

### 2. Retrieve, Don’t Stuff

A common anti-pattern is “context stuffing”  passing all available data into the prompt.

Instead, use **selective retrieval**:
- Identify only the most relevant signals for the current prospect  
- Filter competitor data before passing it to the model  
- Use retrieval or ranking logic to select top-k inputs  

This ensures the model processes only what is necessary.

---

### 3. Preprocess Upstream

Not all data needs to be interpreted by the LLM.

Move computation outside the model:
- Aggregate signals before prompt construction  
- Convert raw data into concise summaries  
- Precompute insights (e.g., “high hiring velocity” instead of raw job counts)

This shifts work from expensive model inference to cheaper deterministic computation.

---

### 4. Reuse Static Context

If parts of your prompt are repeated across requests (e.g., system instructions or templates), avoid recomputing them unnecessarily.

Strategies include:
- Prompt caching (if supported)  
- Separating static and dynamic prompt components  
- Minimizing repeated large context blocks  

This reduces redundant prefill computation across calls.

---

### 5. Structure the Prompt Efficiently

Well-structured prompts are easier for the model to process.

Best practices:
- Place instructions early  
- Use concise bullet points instead of paragraphs  
- Avoid redundant phrasing  

Efficient structure improves both model comprehension and processing speed.

---

## Applying This to `outreach_generator.py`

In Your implementation, the primary source of prefill latency is the **dense competitor gap brief**.

To improve performance, I would:

1. Replace raw competitor briefs with structured summaries  
2. Limit enrichment data to the most relevant signals  
3. Precompute key insights outside the LLM  
4. Log input token count alongside TTFT  

For example:

```python
log_data = {
    "ttft": ttft,
    "input_tokens": input_token_count,
    "output_tokens": output_token_count
}
```
This allows you to directly correlate:

Larger inputs → higher TTFT
Smaller inputs → improved responsiveness

So when prefill is the bottleneck:

The problem is not how fast the model writes  it is how much it has to read.

Reducing input complexity is therefore the most effective way to improve latency.
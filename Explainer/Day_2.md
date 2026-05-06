# Function Calling at the Token Level: Constrained Decoding vs Structured Generation

## Core Idea

When your MCP agent “calls a tool,” the model is not actually executing code itself. It is generating tokens that represent:
- a tool name,
- arguments,
- and a structured format the runtime can interpret.

The important question is:

> Is the model restricted to valid tool outputs during decoding, or is it freely generating structured text that the runtime later parses?

This distinction determines:
- what failures are possible,
- how reliable tool use is,
- and how your retry logic should work.

---

# 1. Structured Generation

In structured generation, the model generates tool calls the same way it generates ordinary text:
- one token at a time,
- autoregressively,
- based on probabilities.

The prompt contains:
- tool definitions,
- schemas,
- and examples.

The runtime later parses the generated output.

Example:

```json
{
  "tool": "hubspot_create_contact",
  "arguments": {
    "email": "prospect@company.com"
  }
}
```

Internally, the model is simply predicting:
- `{`
- `"tool"`
- `"hubspot_create_contact"`
- etc.

The runtime is responsible for validating whether the output is correct.

---

# Failure Modes of Structured Generation

Because generation is unconstrained, several failures are possible.

## Hallucinated Tool Names

The model may generate:

```json
{
  "tool": "hubspot_add_user"
}
```

even if the tool does not exist.

---

## Malformed JSON

Examples:
- missing brackets,
- invalid commas,
- incomplete objects.

---

## Missing Arguments

Example:

```json
{
  "tool": "hubspot_create_contact"
}
```

without required parameters.

---

# Why These Failures Happen

The model is fundamentally:
> predicting probable next tokens.

It is not verifying:
- schema correctness,
- runtime state,
- or whether the tool actually exists.

This means:
- correctness is probabilistic,
- not guaranteed.

---

# 2. Constrained Decoding

Constrained decoding changes this behavior.

Instead of allowing unrestricted token generation, the inference engine restricts which tokens are valid during decoding.

This is commonly implemented through:
- logit masking,
- grammar constraints,
- or schema-constrained decoding.

---

# How Constrained Decoding Works

During generation:
1. the model produces token probabilities (logits),
2. invalid tokens are masked,
3. only valid schema continuations remain available.

For example:

After:

```json
"tool":
```

the runtime may restrict generation to:

```text
hubspot_create_contact
hubspot_search_company
hubspot_update_deal
```

All other tool names are removed from the candidate space.

The model still generates probabilistically, but only within valid boundaries.

---

# What Constrained Decoding Prevents

If constraints are correctly applied:
- invalid tool names become impossible,
- malformed schema structures disappear,
- parsing reliability improves significantly.

This means some failures cannot happen by construction.

For example:
- a `tool-not-found` error may indicate a runtime registry issue rather than a generation failure.

---

# The Hybrid Approach

Modern systems usually combine both approaches.

The model is:
- trained on structured tool-use examples,
- while the runtime applies decoding constraints during inference.

This creates a hybrid pipeline:

```text
Tool-use training
        +
Structured generation
        +
Grammar-constrained decoding
        +
Runtime validation
```

Examples include:
- OpenAI Structured Outputs,
- Outlines,
- Guidance,
- llama.cpp grammars.

---

# Why This Matters for Your MCP Integration

Understanding these mechanics changes how you should debug failures.

If your system mainly relies on structured generation:
- malformed JSON is expected,
- retries are necessary,
- validation becomes critical.

If your system uses constrained decoding:
- many generation failures become impossible,
- failures are more likely caused by:
  - registry synchronization,
  - MCP initialization,
  - or runtime orchestration.

---

# Correct Failure Classification

Instead of treating all failures the same way, your MCP system should separate them into categories.

| Failure Type | Likely Cause | Correct Response |
|---|---|---|
| Malformed JSON | Generation failure | Retry / repair |
| Missing fields | Schema failure | Validation + retry |
| Unknown tool | Registry/runtime issue | Reload or sync registry |
| Tool execution error | Runtime/tool issue | Tool-level retry |
| Timeout | Infrastructure issue | Backoff + retry |

Different failures require different handling strategies.

---

# Practical Improvements for Your System

## 1. Separate Failure Layers

Do not treat all tool-call failures as hallucinations.

Separate:
- generation failures,
- parsing failures,
- registry failures,
- execution failures.

---

## 2. Add Schema Validation Before Execution

Validate:
- required fields,
- argument types,
- tool names

before executing the tool.

---

## 3. Use Constrained Decoding Where Possible

Libraries like:
- Outlines,
- Guidance,
- llama.cpp grammars

can guarantee structurally valid outputs.

This reduces:
- malformed JSON,
- hallucinated tool names,
- invalid schemas.

---

## 4. Improve Runtime Logging

Log separately:
- generated tool name,
- parser result,
- schema validation result,
- execution result,
- MCP registry state.

This makes debugging much easier.

---

## 5. Add Failure-Specific Retry Policies

Different failures should trigger different retries.

Examples:
- malformed JSON → regenerate
- registry failure → reload MCP tools
- execution timeout → retry tool execution

A single retry strategy is not enough.

---

# Key Insight

Function calling is not “magic reasoning.”

It is the interaction between:
- probabilistic token prediction,
- constrained decoding,
- structured generation,
- schema validation,
- and runtime orchestration.

Understanding which layer failed is essential for building reliable MCP-based agents.

Without understanding these mechanics, debugging tool-use systems becomes guesswork.


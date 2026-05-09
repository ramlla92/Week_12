# Question

**Topic: Agent & tool-use internals — function-calling at the token level**

In my Conversion Engine, the HubSpot MCP integration assumes the model "chooses" a tool when it sees the user's intent. But I do not actually know what function-calling is mechanically — is it constrained decoding over a tool-name vocabulary (logit masking at the tool-selection step), or is it unconstrained generation of a structured JSON object that the runtime parses post-hoc and routes? The two have very different failure modes.

# Why this gap

I ship `conversion-engine/agent/` code that depends on this distinction. If it's constrained decoding, my "tool-not-found" errors are impossible by construction and any failure I see is a runtime parsing bug. If it's structured output + parse, I need to defend my retry logic and validate JSON schema enforcement. I currently treat the two as equivalent in my error handling, which is wrong.

# Connection to Week 10/11 Artifact

This gap is grounded in my Week 10/11 implementation of `conversion-engine/agent/` (the MCP integration) and my error logs showing tool-call failures. Understanding whether the failure happens at the **token generation level** (logit masking) or the **post-generation parsing level** (schema validation) is critical for building a resilient agent.

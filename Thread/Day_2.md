URL: https://x.com/akmel3784/status/2052103493402472877?s=20

1/ Function calling in LLM agents is not always “the model choosing a tool.”

Mechanically, two very different things may be happening during inference:
- constrained decoding
- or structured text generation


---

2/ In structured generation, the model freely generates JSON-like outputs autoregressively.

The runtime later:
- parses the JSON
- validates the schema
- routes the tool call

This means malformed JSON and hallucinated tools are possible.

---

3/ In constrained decoding, the inference engine restricts valid tokens during generation.

After:
```json
"tool":
```

the model may only be allowed to generate registered tool names.

Invalid tools become impossible by construction.

---

4/ This distinction changes how MCP agents should be debugged.

- Structured generation → validation + retry heavy
- Constrained decoding → runtime/orchestration failures become more important

Reliable tool use depends on both decoding mechanics and runtime system design.
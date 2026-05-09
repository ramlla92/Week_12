# Day 3
# Title

Verdict Learning vs Explanation Pattern Memorization in Preference-Tuned Classifiers

# The Gap

In preference-tuned classifier-style outputs, responses are often formatted as:
- a structured verdict,
- followed by a natural-language explanation.

However, during training, the loss is applied across all generated tokens equally. This creates a potential ambiguity in what the model is actually learning.

The model may:
- learn the true classification boundary and decision-making logic,
- or simply learn to imitate explanation patterns commonly associated with specific verdicts.

For example, if “Pass” examples frequently contain confident and grounded explanations while “Fail” examples contain cautious or corrective phrasing, the model may rely on stylistic explanation cues rather than understanding the actual decision criteria.

This makes it difficult to determine whether the model learned:
- the underlying reasoning process,
- or only the surface structure of the explanation format.

# The Question

How should training pairs and held-out evaluation sets be designed for a preference-tuned classifier to verify that the model learned the actual verdict signal and decision-making logic, rather than memorizing explanation patterns associated with each label?


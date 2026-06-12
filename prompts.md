# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
System: You are a support ticket classifier. Only output the label (billing, bug, feature_request, or other).
User: Classify this ticket: '{text}'
```

### Few-shot prompt

```
System: You are a support ticket classifier. Only output the label (billing, bug, feature_request, or other).
User:
Ticket: 'My app keeps crashing' -> Label: bug
Ticket: 'Can I get a refund?' -> Label: billing
Ticket: 'I love the new update' -> Label: other
Ticket: '{text}' -> Label:
```

### Chain-of-thought / decomposition prompt

```
System: You are a support ticket classifier. Only output the label (billing, bug, feature_request, or other).
User: Classify this ticket: '{text}'. First, briefly reason about the category, then provide the label on a new line starting with 'Label: '.
```

### Verdict (3–4 sentences)

Few-shot performed the best for the 3B local model, as the explicit examples provided a clear template for the output format and helped resolve ambiguity. Zero-shot occasionally returned full sentences instead of just the label, making parsing more difficult. Chain-of-thought was useful for identifying complex "feature_request" cases by allowing the model to analyze the user's intent first, though it was overkill for simple "billing" or "bug" reports.

## Task 3 — Structured output notes

Ollama's `llama3.2:3b` was surprisingly reliable at producing valid JSON when using the `response_format={"type": "json_object"}` parameter. When the model failed to return a valid label from the allowed list, the Python script defaulted it to "other" to maintain robustness. The local model is significantly faster than hosted Gemini for these small tasks, though it requires more explicit validation of the reasoning logic.

# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
Classify this support ticket into one of these labels: billing, bug, feature_request, other. Return ONLY the single label word lowercased.
Ticket: {text}
```

### Few-shot prompt

```
Classify the ticket into one of these labels: billing, bug, feature_request, other. Return ONLY the single label word lowercased.

Ticket: "I need a way to filter results by date."
Label: feature_request

Ticket: "The checkout page gives a 500 internal server error."
Label: bug

Ticket: "Where is your office located?"
Label: other

Ticket: "{text}"
Label:
```

### Chain-of-thought / decomposition prompt

```
Classify this support ticket into one of these labels: billing, bug, feature_request, other.
First, write a 1-sentence reasoning, then on the very last line write 'Final Label: <label>'.
Ticket: {text}
```

### Verdict (3–4 sentences)

All three patterns produced correct labels on the clear-cut tickets (billing, bug, feature_request). Zero-shot was the simplest but occasionally returned extra words on ambiguous `other` tickets like #4 and #8, breaking the parser. Few-shot was the most format-stable — the three examples acted as an anchor, so the model consistently returned a single clean word. Chain-of-thought helped most on borderline cases (e.g. ticket #4 which could be `other` or a UX issue) but needed extra parsing logic to extract the final label.

## Task 3 — Structured output notes

The local Ollama model (Llama 3.2:3b) successfully generated a structurally valid JSON format without crashing the pipeline, but its semantic reliability within that structure was flawed, consistently outputting a `0.0` value for the confidence field instead of dynamically adjusting it based on the ticket content. Because Gemini hit free-tier rate limits initially, the local model had to handle the entirety of the schema validation, proving that while lightweight models can maintain basic syntax under low temperatures, they require programmatic fallbacks (like our Python validation block) to catch placeholder values and ensure stable real-world extraction.
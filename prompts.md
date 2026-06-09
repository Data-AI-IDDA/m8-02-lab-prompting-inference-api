# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
Classify this support ticket into one of: billing, bug, feature_request, other.
Reply with only the label.

Ticket: {text}
```

### Few-shot prompt

```
Classify support tickets into: billing, bug, feature_request, other.

Ticket: "I was charged twice this month." → billing
Ticket: "The app crashes when I upload a file." → bug
Ticket: "Can you add dark mode?" → feature_request
Ticket: "Where is my order confirmation?" → other

Ticket: "{text}" →
```

### Chain-of-thought / decomposition prompt

```
Classify this support ticket into: billing, bug, feature_request, other.
Think step by step, then give the final label.

Ticket: {text}
Answer:
```

### Verdict (3–4 sentences)

> Zero-shot was the most reliable pattern for this task — it returned clean,
> parseable labels in most cases. Few-shot produced correct classifications
> but the small local model (llama3.2:3b) ignored the format and added
> unnecessary explanations. Chain-of-thought reasoning was logical but
> output was too verbose to parse directly. Ambiguous tickets like
> "password reset" and "invoice copy" caused misclassification across all
> three patterns, showing that label definitions matter as much as prompt style.

## Task 3 — Structured output notes

> The local Ollama model (llama3.2:3b) initially failed to produce valid JSON —
> it returned the schema description literally instead of real values.
> After adding a concrete example output to the prompt, all tickets returned
> valid, parseable JSON. Gemini has native JSON mode which is more reliable
> out of the box and requires less prompt engineering to follow format instructions.
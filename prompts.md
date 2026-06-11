# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
Label this ticket as one of billing, bug, feature_request, other. Reply with the label only.
Ticket: <text>
```

### Few-shot prompt

```
Ticket: charged twice
Label: billing

Ticket: app crashes
Label: bug

Ticket: add dark mode
Label: feature_request

Ticket: where is the guide
Label: other

Ticket: <text>
Label:
```

### Chain-of-thought / decomposition prompt

```
Ticket: <text>

Think briefly, then write 'Label: <one of billing, bug, feature_request, other>'
```

### Verdict (3–4 sentences)

> Few-shot was the most consistent across all four categories — the examples
> anchored the model's output format and reduced label confusion on ambiguous
> tickets like refund requests (billing vs other) and UI complaints
> (bug vs feature_request). Zero-shot performed well on clear-cut cases but
> occasionally mislabelled edge cases where the ticket straddled two categories.
> CoT produced the most reasoning but that verbosity introduced noise: the model
> sometimes hedged between two labels before committing, and on short tickets
> the extra thinking added no accuracy benefit. For a production classifier,
> few-shot at low temperature is the best default; CoT is worth keeping as a
> fallback for low-confidence cases.

## Task 3 — Structured output notes

> Gemini returned valid, schema-conforming JSON on every call because constrained
> decoding enforces the schema at the token level, leaving no room for malformed
> output. The local model followed the format most of the time but occasionally
> wrapped the JSON in markdown fences or dropped a required key; a fence-stripping
> regex and a `validate()` fallback with a safe default dict handled both cases.

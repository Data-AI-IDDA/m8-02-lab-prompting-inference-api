# Prompts & Bake-off Notes

These are the exact prompts used in `llm_classifier.ipynb`. Labels are always one of
`billing`, `bug`, `feature_request`, `other`. `{ticket}` is the customer message.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
Classify the support ticket into exactly one label: billing, bug, feature_request, other.
Reply with only the label.

Ticket: {ticket}
```

### Few-shot prompt

```
Classify the support ticket into exactly one label: billing, bug, feature_request, other.
Reply with only the label.

Examples:
Ticket: "You charged my card three times for one order." -> billing
Ticket: "The page goes blank when I click save." -> bug
Ticket: "Please add a way to schedule reports." -> feature_request
Ticket: "Where can I find your privacy policy?" -> other

Ticket: "{ticket}" ->
```

### Decomposition / chain-of-thought prompt

```
Classify the support ticket into exactly one label: billing, bug, feature_request, other.
First give a one-sentence rationale, then the final label.
Use exactly this format:
Rationale: <one short sentence>
Label: <one of the labels>

Ticket: {ticket}
```

### Verdict (3–4 sentences)

Few-shot was the most consistent: the labeled examples pin down what each category means, so
it handled the tricky billing/bug ticket (#5, the invoice page error) better than the others.
Zero-shot is the simplest and is fine on clear tickets, but it's the most likely to slip on
ambiguous ones — the "export reports as CSV" ticket (#6) can drift to `other` instead of
`feature_request`. Decomposition helped most on the genuinely ambiguous tickets because the
model commits to a short rationale first, but it costs more tokens and the output needs
parsing. Net: few-shot for reliability, decomposition as a tie-breaker on hard cases.

## Task 3 — Structured output

### Structured JSON prompt (Gemini, JSON mode, temperature 0.0)

```
Classify the support ticket. Return JSON with keys:
  label: one of billing, bug, feature_request, other
  confidence: a number between 0 and 1
  reason: a short string

Ticket: {ticket}
```

System instruction: `You are a concise support-ticket classifier.`
The call also sets `response_mime_type="application/json"` and a `response_schema`
(`{label, confidence, reason}`), so the model returns parseable JSON.

### Ollama / local prompt (`llama3.2:3b`, `format=json`, temperature 0.0)

```
Classify the support ticket into one label: billing, bug, feature_request, other.
Return ONLY JSON, no extra text: {"label": "...", "confidence": 0.0, "reason": "..."}

Ticket: {ticket}
```

### Notes (Gemini vs local)

Gemini returned valid JSON matching the schema almost every time, so the validator rarely had
to reject anything. The local model is slower and more likely to wander outside the contract —
extra prose around the JSON, a label outside the allowed set, or confidence as a string — so
the same `validate_classification` check catches more failures there. Local is a fine offline
fallback but needs stronger validation/repair before you'd trust it.

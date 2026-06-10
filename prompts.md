# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```text
Classify the customer support ticket into exactly one of these labels:

- billing
- bug
- feature_request
- other

Return only the label.

Ticket:
{text}
```

### Few-shot prompt

```text
Classify the customer support ticket into exactly one of these labels:

- billing
- bug
- feature_request
- other

Examples:

Ticket: I was charged twice for my subscription this month.
Label: billing

Ticket: The app crashes whenever I open it after the latest update.
Label: bug

Ticket: Please add dark mode to the dashboard.
Label: feature_request

Ticket: Just wanted to say the new UI looks really clean.
Label: other

Return only the label.

Ticket:
{text}

Label:
```

### Chain-of-thought / decomposition prompt

```text
Classify the customer support ticket into exactly one of these labels:

- billing
- bug
- feature_request
- other

Briefly identify the main signal in the ticket, then give the final label.

Use this exact format:
Signal: short phrase
Label: one label only

Ticket:
{text}
```

### Verdict

The few-shot prompt performed best in this bake-off. It correctly classified the obvious billing, bug, and feature request tickets, and it also handled the password reset question as `other`, which was the best available label. The zero-shot prompt worked well for most simple tickets, but it mislabeled the password reset question as `feature_request`. The decomposition prompt was easier to inspect because it showed a signal before the label, but it failed on the password reset ticket and the UI compliment, so it was less reliable than few-shot in this run.

## Task 3 — Structured output notes

Gemini could not be tested live because the API returned a `429 RESOURCE_EXHAUSTED` quota error, so I used the local Ollama `llama3.2:3b` fallback. Ollama produced valid JSON for all 8 tickets after I used a strict JSON prompt and `response_format={"type": "json_object"}`.

I still validated every response in Python by checking that the label was one of the allowed categories, confidence was between 0 and 1, and reason was a non-empty string. I also tested bad outputs manually, including invalid JSON, unsupported labels, out-of-range confidence, and missing fields, and the validator rejected them gracefully.
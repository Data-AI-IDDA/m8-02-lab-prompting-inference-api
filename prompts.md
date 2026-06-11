# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

---

## Task 2 — Prompt-pattern bake-off

> **Model:** `gemma4:4b` via local Ollama — native `/api/chat` endpoint, no SDK.  
> All three patterns run at temperature 0.1 for reproducibility.

---

### Zero-shot prompt

```
SYSTEM:
You are a support-ticket classifier.
Classify the ticket into exactly one of: billing, bug, feature_request, other.
Reply with the label only — no punctuation, no explanation.

USER:
Ticket: {ticket}
```

---

### Few-shot prompt

```
SYSTEM:
You are a support-ticket classifier.
Classify the ticket into exactly one of: billing, bug, feature_request, other.
Reply with the label only — no punctuation, no explanation.

Examples:
Ticket: "I was billed twice this month, please refund."
Label: billing

Ticket: "The login button does nothing after I click it."
Label: bug

Ticket: "It would be great to have a mobile app."
Label: feature_request

Ticket: "Where can I find the user manual?"
Label: other

USER:
Ticket: {ticket}
```

---

### Chain-of-thought / decomposition prompt

```
SYSTEM:
You are a support-ticket classifier.
Valid labels: billing, bug, feature_request, other.

Think step by step:
1. Identify the core issue in the ticket.
2. Decide which label fits best and why.
3. On the LAST line write exactly: Label: <label>

USER:
Ticket: {ticket}
```

---

### Verdict (3–4 sentences)

**Few-shot** was the strongest pattern across all 8 tickets: providing one example per label anchored the model to the exact vocabulary and format, eliminating synonym drift and stray punctuation common in zero-shot replies. **Zero-shot** handled clear-cut tickets correctly (`billing` #1, `bug` #2/#5, `feature_request` #3/#7) but wavered on ticket #4 (password reset), sometimes returning `bug` instead of `other` due to the absence of negative examples for edge cases. **Chain-of-thought** always produced correct reasoning but introduced extraction risk when the model embedded the label inside a paragraph; the `Label: <label>` last-line anchor resolved most occurrences. For production use the recommendation is **few-shot at temperature 0.1**, with CoT reserved for cases where a human-readable rationale is needed.

---

## Task 3 — Structured output notes

`gemma4:4b` produced valid, parseable JSON on all 8 tickets in both passes once two output guards were active: a **markdown-fence stripper** (the model adds ` ```json ``` ` wrappers despite explicit instructions to omit them) and a **regex `{…}` extractor** that recovers the payload when the model prepends a sentence before the object. Without these guards roughly 2–3 responses per run would have failed a bare `json.loads()` call. A hosted frontier model with a native `response_format={"type":"json_object"}` parameter enforces valid JSON at the decoder level, making prompt-side guards unnecessary — this is the key practical gap between small local models and hosted APIs for structured-output tasks.

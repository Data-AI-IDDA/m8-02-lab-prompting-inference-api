# Prompts & Bake-off Notes

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
SYSTEM:
You are a support-ticket classifier.
Classify the customer message into exactly one of these labels:
billing, bug, feature_request, other.
Reply with the label only — no punctuation, no explanation.

USER:
Message: "<ticket text>"
```

### Few-shot prompt

```
SYSTEM:
You are a support-ticket classifier.
Classify the customer message into exactly one of these labels:
billing, bug, feature_request, other.
Reply with the label only — no punctuation, no explanation.

Examples:
Message: "I was charged twice last month."
Label: billing

Message: "The app crashes when I open the settings page."
Label: bug

Message: "Please add a bulk-export feature to the dashboard."
Label: feature_request

Message: "Just wanted to say your team is fantastic!"
Label: other

USER:
Message: "<ticket text>"
```

### Chain-of-thought / decomposition prompt

```
SYSTEM:
You are a support-ticket classifier.
Available labels: billing, bug, feature_request, other.

For each message:
1. Identify the core problem in one sentence.
2. Ask: Is money/payments involved? → billing
         Is something broken/not working? → bug
         Is a new capability requested? → feature_request
         Otherwise → other
3. Output your final answer on the last line as: LABEL: <label>

USER:
Message: "<ticket text>"
```

### Verdict (3–4 sentences)

> **Few-shot won** with the highest accuracy across all ticket categories because
> the in-context examples removed ambiguity for edge cases — particularly the
> "other" category (praise messages) and "billing" tickets that mention "charge"
> without being phrasing the model had been primed to recognise.
> Zero-shot performed well on clear-cut bugs and feature requests but mis-labelled
> two "billing" tickets as "other" because the word "charge" appeared in an
> ambiguous sentence.
> Chain-of-thought was the most verbose but achieved comparable accuracy to few-shot;
> its main advantage was transparency — the reasoning step made it obvious *why* a
> borderline ticket like "I can't log in" was labelled "bug" rather than "other".
> For a production classifier, few-shot is the pragmatic default; CoT is valuable
> when you need an audit trail or when confidence is low.

---

## Task 3 — Structured output notes

> Gemini 2.0 Flash produced valid JSON on all 8 tickets with no failures; the only
> post-processing needed was stripping the occasional ` ```json ` fence.
> The local llama3.2:3b model (via Ollama) was less consistent: it prepended
> explanatory text in 1–2 cases, and once returned a `confidence` of `1.5`, which
> the validator caught and handled by falling back to `label="other", confidence=0.0`.

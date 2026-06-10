# Prompts & Bake-off Notes

## Task 2 — Prompt-pattern bake-off

All three classifiers used the system message:
`"You are a precise support ticket classifier."`
and `temperature=0.2`. The label list passed to every prompt:
`"billing", "bug", "feature_request", "other"`.

---

### Zero-shot prompt

```
Classify this customer support ticket into exactly one of: "billing", "bug", "feature_request", "other".
Reply with only the label — no explanation, no punctuation.

Ticket: {ticket text}
```

---

### Few-shot prompt

```
Classify customer support tickets into one of: "billing", "bug", "feature_request", "other".

Ticket: It would be great if you could add a dark mode to the dashboard.
Label: feature_request

Ticket: How do I reset my password? I can't find the link anywhere.
Label: other

Ticket: The app crashes on startup after the latest update on Android 14.
Label: bug

Ticket: Can you send me a copy of my last invoice for our accounting team?
Label: billing

Ticket: {ticket text}
Label:
```

---

### Chain-of-thought / decomposition prompt

```
Classify the following support ticket into one of: "billing", "bug", "feature_request", "other".

Think step by step:
1. What is the customer experiencing or asking for?
2. Which category best fits?
3. State the final label on a line starting with 'Label:'

Ticket: {ticket text}
```

---

### Verdict (3–4 sentences)

Zero-shot and chain-of-thought agreed on every ticket and produced the same five-of-six result; both correctly identified the sarcastic bug report (ticket 4) and the complaint-framed feature request (ticket 6), where surface tone could have misled a simpler prompt. Few-shot failed on four of six tickets, returning `billing` for every input including a clear 500-error bug and an export-limit feature request — the four labeled examples ended on a billing example, and recency bias in the context anchored the model to that label regardless of input. Chain-of-thought matched zero-shot exactly on all six decisions, adding structured reasoning steps without changing any label, which suggests decomposition overhead is not worth the extra tokens on a four-class problem this well-defined.

---

## Task 3 — Structured output notes

`llama3.2:3b` with `response_format={"type": "json_object"}` enforced produced valid, parseable JSON on all 6 tickets across both runs (primary client and explicit Ollama route), with all three required fields present and correctly typed every time. The intended Gemini-vs-Ollama comparison was not possible: calls to `gemini-2.0-flash` returned `limit: 0` across all quota metrics — initially diagnosed as an account-level restriction, but the actual cause is that `gemini-2.0-flash` was deprecated in February 2026 and retired on March 3, 2026, leaving its free-tier quota permanently at zero. Both Task 3 runs therefore hit the same local model, turning the comparison into enforcement-on vs enforcement-off: the failure-mode demo in the notebook confirms that without `response_format`, the model returns plain text prose rather than JSON, which is exactly what makes structured-output enforcement necessary.
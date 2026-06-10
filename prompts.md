# Prompts used in Task 2 — Bake-off

---

## Zero-shot prompt

```
Classify the following support ticket into exactly one of these labels:
billing, bug, feature_request, other.
Reply with the label only.

Ticket: {ticket_text}
```

---

## Few-shot prompt

```
Classify the following support ticket into exactly one of these labels:
billing, bug, feature_request, other.
Reply with the label only.

Examples:
Ticket: "My card was charged but I never received a receipt." → billing
Ticket: "The login button stops working after 3 failed attempts." → bug
Ticket: "Please add an option to export reports as PDF." → feature_request

Ticket: {ticket_text}
```

---

## Chain-of-thought prompt

```
You are a support ticket classifier. Labels: billing, bug, feature_request, other.

Think step by step:
1. What is the user's main problem?
2. Does it involve a payment or invoice? → billing
   Does it describe broken or incorrect behavior? → bug
   Is it a request for a new capability? → feature_request
   Otherwise → other
3. State your final answer as: Label: <label>

Ticket: {ticket_text}
```

---

## Verdict

**Few-shot** was the most reliable pattern overall: providing three labeled examples anchored the model to the exact output vocabulary and eliminated label drift (e.g. returning `"Bug report"` instead of `"bug"`). **Chain-of-thought** handled ambiguous tickets best — for example a password-reset ticket that sits between `bug` and `other` — because the explicit reasoning steps forced the model to commit to a decision path, but it required post-processing to extract the final label from the free-text response. **Zero-shot** was the fastest and still correct on unambiguous tickets, yet it occasionally added punctuation or used non-canonical casing, making downstream parsing brittle without a `.strip().lower()` fix. For a production classifier, **few-shot with a strict system instruction** is the practical winner; chain-of-thought is worth adding only when a confidence score or justification is also required.

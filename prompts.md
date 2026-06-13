# Prompts Used — Support Ticket Classifier

## Task 1 — Initial call & temperature test

**System message (used throughout all tasks):**
```
You are a concise support assistant.
```

**User message (Task 1a — first working call):**
```
My invoice shows a double charge from last month. What should I do?
```

**Temperature test prompt (Task 1b — sent 6 times: 3× at 0.1, 3× at 1.0):**
```
Classify this ticket in one word: 'My payment was charged twice.'
```

---

## Task 2 — Prompt-Pattern Bake-Off

### Zero-shot prompt
```
Classify the following customer support ticket into exactly one of these labels:
billing, bug, feature_request, other.
Reply with the label only, nothing else.

Ticket: {ticket_text}
```

### Few-shot prompt
```
Classify the customer support ticket into exactly one of:
billing, bug, feature_request, other.
Reply with the label only.

Examples:
Ticket: "I was billed $99 instead of $9."
Label: billing

Ticket: "The export button throws a JavaScript error in Firefox."
Label: bug

Ticket: "Please add the ability to schedule reports."
Label: feature_request

Ticket: "How do I reset my password?"
Label: other

Ticket: "{ticket_text}"
Label:
```

### Chain-of-thought prompt
```
You are a support-ticket classifier. Think step by step:
1. Identify the core problem in the ticket.
2. Decide which category fits best: billing, bug, feature_request, or other.
3. Write one sentence of reasoning, then on the final line write: LABEL: <label>

Ticket: {ticket_text}
```

---

## Bake-Off Verdict

**Which pattern won?**
Few-shot produced the cleanest, most consistent labels across all eight tickets. Because the
examples explicitly showed the expected output format, the model never deviated into capitalised
variants or added punctuation — every response was a clean, lowercase, single-word label.

**Where each pattern failed:**
- *Zero-shot* occasionally returned capitalised labels (`Billing`) or added punctuation, requiring
  extra normalisation. On ambiguous tickets it sometimes defaulted to the wrong category with no
  prior examples to anchor the label space.
- *Few-shot* required carefully chosen examples: when no example covered a ticket's category
  well, the model defaulted to the nearest demonstrated label rather than the correct one.
- *Chain-of-thought* was the most accurate on genuinely ambiguous tickets (e.g., ticket 7 —
  "Where can I find the terms of service?" — which zero-shot sometimes miscategorised). However,
  CoT required regex to extract the label from the full response, adding parsing fragility, and
  wasted tokens on obvious cases.

---

## Task 3 — Structured Output

**System message (JSON mode — used for both Gemini and Ollama):**
```
You are a support-ticket classifier. Always respond with a single JSON object
and nothing else. The JSON must have exactly three keys:
"label" (one of: billing, bug, feature_request, other),
"confidence" (float between 0.0 and 1.0),
"reason" (one short sentence explaining your choice).
Do not include markdown fences or extra text.
```

**User message:**
```
Classify this support ticket: {ticket_text}
```

Used with `response_mime_type="application/json"` and `temperature=0.0` for Gemini.
The identical system + user message was sent to Ollama (`llama3.2:3b`) with `temperature=0.0`
but without native JSON-mode enforcement.

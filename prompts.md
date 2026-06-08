# Prompt Log — LLM Classifier Lab

---

## Task 1 — First call

**System message**
```
You are a concise support assistant.
```

**User message**
```
Classify this support ticket in one word (billing/bug/feature_request/other):
'I was charged twice this month.'
```

**Temperature sweep prompt** (same system message, used at 0.1 ×3 and 1.0 ×3)
```
Classify this support ticket with exactly one label
(billing / bug / feature_request / other) and a one-sentence reason.

Ticket: 'The export to CSV button does nothing when I click it.'
```

---

## Task 2 — Prompt patterns

### Zero-shot prompt
```
Classify the following customer support ticket into exactly one of these labels:
billing, bug, feature_request, other.

Reply with only the label — no punctuation, no explanation.

Ticket: {ticket}
```

### Few-shot prompt
```
Classify the customer support ticket into exactly one of these labels:
billing, bug, feature_request, other.

Here are some examples:

Ticket: "My invoice shows an incorrect amount."
Label: billing

Ticket: "The search bar freezes the whole page when I type."
Label: bug

Ticket: "Please add the ability to export reports as PDF."
Label: feature_request

Now classify:
Ticket: {ticket}
Label:
```

### Chain-of-thought prompt
```
Classify the following support ticket. Think step by step:
1. What is the customer's core problem or request?
2. Does it involve money/invoices (billing), a broken feature (bug),
   a new capability ask (feature_request), or something else (other)?
3. State your final label on the last line as: Label: <label>

Ticket: {ticket}
```

---

## Task 2 — Bake-off verdict

All three patterns scored 8/8 on this ticket set with Claude claude-sonnet-4-20250514. The meaningful differences were in **output format reliability** and **debuggability**, not raw accuracy.

**Zero-shot** is the most fragile: with no examples or reasoning structure, the model relies entirely on its training to infer the desired output format. On a ticket like "My password reset email never arrived" — which could be read as a bug (email system broken) or `other` (account access issue) — zero-shot gave the correct answer but would be the first pattern to fail on an ambiguous or out-of-distribution ticket. It also occasionally adds a short explanation despite being told not to, which breaks downstream parsing.

**Few-shot** was the most reliable for format compliance. By demonstrating three `Label: <word>` completions, the model consistently terminated with just the label. The examples also disambiguated the `other` category: showing a non-billing/bug/feature ticket in the examples anchored the model toward the right class for tickets that don't fit neatly. The cost is that the few-shot examples consume ~60–80 extra input tokens per call.

**Chain-of-thought** produced the most auditable outputs. The step-by-step reasoning made every decision inspectable — when the model classified "password reset email" as `other` rather than `bug`, the reasoning ("this is an account access delivery issue, not a software defect") was visible and correct. The tradeoff is ~4–6× more output tokens and the need to parse the final label from free text with a regex. For production use, CoT is best reserved for low-volume, high-stakes classifications where a wrong label has real consequences and auditability matters.

**Winner:** Few-shot for production (best format compliance, low token overhead). Chain-of-thought for debugging and ambiguous-case analysis.

---

## Task 3 — Structured output prompt

**System message**
```
You are a support-ticket classifier. Always respond with valid JSON only.
```

**User message**
```
You are a support-ticket classifier.
Classify the ticket and respond with ONLY a valid JSON object — no markdown fences,
no explanation outside the JSON.

Schema:
{
  "label":      "billing | bug | feature_request | other",
  "confidence": <float 0.0–1.0>,
  "reason":     "<one short sentence>"
}

Ticket: {ticket}
```

**Parameters:** `temperature=0.0`, `max_tokens=128`

**Design notes:**
- Temperature 0 is used to maximise JSON format compliance and reduce label variance.
- The schema is included inline in the prompt rather than via a tool/function-calling API because the task requires the `confidence` and `reason` fields, which are not natural tool parameters.
- Output is defensively cleaned: markdown fences are stripped before `json.loads()` to handle the most common small-model formatting error.
- Validation checks both that `label` is in `{"billing","bug","feature_request","other"}` and that `confidence` is a float in `[0.0, 1.0]`.

# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt


```
Classify the following support ticket into exactly one of these labels: billing, bug, feature_request, other.

Respond with ONLY the label, nothing else.
Message: "{text}"

Label:
```

### Few-shot prompt

```
Classify the following support ticket into exactly one of these labels: billing, bug, feature_request, other.

Respond with ONLY the label, nothing else.
Example 1:

Message: "I can't log in, it says invalid password but I'm sure it's correct."

Label: bug
Example 2:

Message: "My card was charged but I never got an order confirmation."

Label: billing
Example 3:

Message: "Could you add the ability to export data to Excel?"

Label: feature_request
Now classify this message:

Message: "{text}"

Label:
```

### Chain-of-thought / decomposition prompt

```
Classify the following support ticket into exactly one of these labels: billing, bug, feature_request, other.
First, briefly reason about what the message is asking for (1-2 sentences).

Then, on a new line, write "Label: " followed by ONLY the label.
Message: "{text}"
```

### Verdict (3–4 sentences)

Zero-shot and few-shot produced identical labels across all 8 tickets, suggesting that for this task the model already understands the category boundaries well enough that adding labeled examples doesn't change its decisions — few-shot just adds extra tokens with no accuracy gain here. Chain-of-thought diverged on two tickets: it mislabeled ticket 1 (a billing refund request for a double charge) as "bug", likely because the reasoning fixated on the word "charged" as a technical error rather than a billing dispute. On ticket 8 (a compliment with no actionable request), CoT labeled it "other" while zero-shot/few-shot both said "feature_request" — arguably CoT got this one right where the simpler prompts didn't. Overall, no pattern clearly "won": zero-shot is the most token-efficient with equal accuracy to few-shot, while CoT's extra reasoning fixed one ambiguous case (ticket 8) but introduced a new error on another (ticket 1), at the cost of significantly more tokens per call.

## Task 3 — Structured output notes

## Task 3 — Structured output notes

Gemini could not be tested due to persistent free-tier quota errors across multiple API keys, so this task was completed entirely with the local Ollama model (`llama3.2:3b`) via its OpenAI-compatible endpoint. The first prompt version — which described the schema using `"label": "billing|bug|feature_request|other"` — caused the model to copy that placeholder string literally into the `label` field (e.g. `"billing|bug"`), which failed validation on most tickets and was corrected to `"other"`. After rewriting the prompt to remove the `|` syntax, spell out the allowed values in plain English, and add a concrete example JSON object, the model returned 8/8 valid JSON objects with correctly-typed `label` (one of the four allowed values) and `confidence` (0–1 float), requiring no fallback corrections. This suggests that for `llama3.2:3b`, prompt phrasing — not just temperature — has a major effect on JSON reliability: schema-style placeholder syntax confuses the model, while explicit instructions plus a worked example produce consistently parseable output. Without a working Gemini key, a hosted-vs-local reliability comparison could not be made directly, but the local model's near-total recovery after the prompt fix suggests `llama3.2:3b` is capable of reliable structured output when given an unambiguous, example-based prompt.

# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```
Classify the following support ticket into exactly one label:

Labels:
billing
bug
feature_request
other

Ticket:
{ticket}

Return only the label.
```

### Few-shot prompt

```
Classify support tickets.

Ticket: I was charged twice this month.
Label: billing

Ticket: The app crashes when I click login.
Label: bug

Ticket: Please add dark mode support.
Label: feature_request

Ticket:
{ticket}

Label:
```

### Chain-of-thought / decomposition prompt

```
Classify the support ticket.

Available labels:
billing, bug, feature_request, other

First briefly determine what the ticket is about.
Then choose the best label.

Ticket:
{ticket}

Format:

Reasoning:
Label:
```

### Verdict (3–4 sentences)

## Verdict

Few-shot prompting performed best overall because the examples helped the model consistently distinguish between billing, bug, feature_request, and other tickets. Zero-shot prompting correctly classified most clear billing and bug requests but occasionally misclassified less specific feature_request or other tickets due to limited context. Chain-of-thought prompting sometimes improved decisions on ambiguous tickets by reasoning through the category, but the extra explanation could introduce inconsistent outputs that required additional parsing. Overall, few-shot prompting provided the most accurate and reliable classifications across all ticket categories.

ticket categories (billing / bug / feature_request / other).

## Task 3 — Structured output notes


Gemini could not be tested because the API quota was unavailable (429 RESOURCE_EXHAUSTED). Based on the API documentation, hosted models with native structured-output support are expected to follow JSON schemas more reliably.

The local Ollama model generally produced valid JSON, but it still required prompt engineering, validation checks, and error handling. When the output was malformed or failed validation, I handled the error gracefully using `try/except` and returned a fallback response.

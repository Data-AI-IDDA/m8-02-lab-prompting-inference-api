# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off
## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

You are a support ticket classifier.

Classify the ticket into one of the following categories:
- billing
- bug
- feature_request
- other

Ticket: {ticket}

Return only the label (no explanation, no extra text).


---

### Few-shot prompt

You are a support ticket classifier.

Classify the ticket into one of the following categories:
- billing
- bug
- feature_request
- other

Examples:

Ticket: I was charged twice this month  
Label: billing

Ticket: The app crashes when I upload a photo  
Label: bug

Ticket: Please add dark mode  
Label: feature_request

Ticket: I cannot log into my account  
Label: bug

Now classify the following ticket:

Ticket: {ticket}

Return only the label (no explanation).


---

### Chain-of-thought / decomposition prompt

You are a support ticket classifier.

Step 1: Read the ticket carefully and understand the user's issue.  
Step 2: Identify whether it is related to billing, a bug, a feature request, or something else.  
Step 3: Decide the final category.

Categories:
- billing (payment, refund, charge issues)
- bug (errors, crashes, broken features)
- feature_request (requests for new features or improvements)
- other (anything not fitting above)

Ticket: {ticket}

Think briefly about the reasoning internally, then output ONLY:

Label: <billing | bug | feature_request | other>


---

### Verdict (3–4 sentences)

Few-shot prompting performed the best overall because it provides clear labeled examples that guide the model toward consistent classification. Zero-shot prompting is simpler but sometimes fails on ambiguous tickets due to lack of context or examples. Chain-of-thought improves reasoning quality but can produce longer or less structured outputs, which is less suitable for strict label-only tasks. Overall, few-shot is the most reliable and stable approach for this classification problem.

## Task 3 — Structured output notes

Gemini was generally more reliable at producing valid JSON outputs that matched the required schema, especially when using low temperature and explicit formatting instructions. The local Ollama model was less consistent and sometimes returned malformed or extra text outside of JSON.

When invalid outputs occurred, I handled them using a JSON parsing step with try/except; if parsing failed or validation did not pass (invalid label or confidence range), I replaced the result with a safe fallback label ("other") and confidence 0.0.
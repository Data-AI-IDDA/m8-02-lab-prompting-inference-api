# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

```text
Ticket text: '{text}'
Respond with only the exact label name from the allowed list.
```

### Few-shot prompt

```text
Classify the following tickets based on these examples:

Ticket: 'Can I get a refund for last month?' -> billing

Ticket: 'The application freezes when uploading a file.' -> bug

Ticket: 'We really need a way to export data to Excel.' -> feature_request

Ticket: '{text}' ->
```

### Chain-of-thought / decomposition prompt

```text
Analyze this ticket step-by-step: '{text}'.

First, explain your reasoning in one short sentence.

Second, provide the final label on a new line in this exact format:
'Label: <label_name>'
```

### Verdict (3–4 sentences)

The few-shot and chain-of-thought prompts performed best overall, while the zero-shot approach depended more heavily on how clearly the ticket matched a category. Billing and bug tickets were classified correctly by all three methods because the wording was direct and specific. Feature request tickets were also handled reliably, especially in the few-shot setup where the examples helped distinguish requests from bug reports. The “other” category was the most difficult because general questions and feedback messages could sometimes overlap with other categories depending on interpretation.


## Task 3 — Structured output notes

> The hosted Google Gemini API (`gemma-4-26b-a4b-it`) produced valid JSON very reliably and consistently followed the required schema. In contrast, smaller local models running through Ollama sometimes generated formatting issues such as missing brackets or trailing commas. To handle this, a Python try-except block was used to catch JSON decoding errors and provide a fallback response when parsing failed.

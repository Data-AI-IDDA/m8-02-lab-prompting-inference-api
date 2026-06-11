# Prompts & Bake-off Notes

Record the exact prompts you used and your verdict here. This file is graded.

## Task 2 — Prompt-pattern bake-off

### Zero-shot prompt

**System:** You are a support ticket classifier. Reply with one word only: billing, bug, feature_request, or other.

**User:** Classify this ticket: {ticket}

---

### Few-shot prompt

**System:** You are a support ticket classifier. Reply with one word only: billing, bug, feature_request, or other.

**User:**

Here are some examples:

Ticket: "I was charged twice last month." → billing

Ticket: "The login button does not work on mobile." → bug

Ticket: "Please add a calendar view to the app." → feature_request

Ticket: "Where is your office located?" → other

Now classify this ticket: {ticket}

---

### Chain-of-thought / decomposition prompt

**System:** You are a support ticket classifier.

**User:**

Classify this support ticket into one of: billing, bug, feature_request, other.

First, think briefly about what the user is asking.

Then write your final answer on the last line in this format — Label: `<label>`

Ticket: {ticket}

---

### Verdict (3–4 sentences)

Few-shot was the most accurate method — the labeled examples helped the model correctly classify edge cases like ticket 1 (double charge → billing) and ticket 6 (invoice copy request → billing), which zero-shot got wrong by focusing on surface keywords instead of intent. Zero-shot struggled with billing tickets because it lacked context for what billing means in this domain. Chain-of-thought matched few-shot on most tickets but required extra parsing logic and still failed on ticket 4 ("reset my password"), which was genuinely ambiguous between other and bug across all three methods. Overall, few-shot gave the best balance of accuracy and output consistency for this classification task.

---

## Task 3 — Structured output notes

The local Ollama model (llama3.2:3b) produced valid JSON in 7 out of 8 cases
when json_object mode was enabled, but on the first attempt it copied the schema
description literally as a label value ("billing | bug"), which was caught by
the validation logic and returned a safe fallback. Classification accuracy was
lower than expected on ambiguous tickets — a larger hosted model would likely
follow the schema and classify edge cases more reliably.
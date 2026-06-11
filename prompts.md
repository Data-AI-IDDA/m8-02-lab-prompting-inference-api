# LLM Prompt Experiments — Support Ticket Classification

We tested three prompt strategies to classify support tickets into:
- billing
- bug
- feature_request
- other

---

# 1. Zero-shot Prompt

We directly asked the model to classify without examples.

Prompt: 

Classify this support ticket into one label:

billing
bug
feature_request
other

Ticket:
{ticket}

Label:


---

# 2. Few-shot Prompt

We provided examples to guide the model.

Prompt:

Prompt:

Examples:

Ticket: I want a refund.
Label: billing

Ticket: The app crashes after login.
Label: bug

Ticket: Please add dark mode.
Label: feature_request

Now classify:

Ticket:
{ticket}

Label:


---

# 3. Chain-of-thought Prompt

We asked the model to briefly reason before giving the label.

Prompt:

Prompt:

Think briefly about the category of the ticket.

Categories:
billing
bug
feature_request
other

Ticket:
{ticket}

Explain in one short sentence, then give the label. 

---

# Comparison Summary (Verdict)

We tested all three approaches on multiple support tickets.

- **Zero-shot**: Simple but sometimes confused similar categories (e.g., bug vs general issue).
- **Few-shot**: Most accurate and consistent because examples clearly defined boundaries between categories.
- **Chain-of-thought**: Improved reasoning but sometimes produced longer outputs that were harder to parse.

### Final conclusion:
Few-shot prompting performed best overall because it provided clear structure and reduced ambiguity, making classification more stable across all tickets. 
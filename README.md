![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# Lab | Your First LLM-Powered Tool

## Overview

Today the LLM stops being a concept and becomes a function you call. You'll make real API calls to a hosted model, steer its output with sampling parameters, run a prompt-pattern bake-off, and force **structured JSON output** so your code can actually use the result. Then you'll run the same prompt against a local model to feel the hosted-vs-local gap.

We use **Google's Gemini API** because it has a genuinely free tier and a clean SDK — no credit card needed. You'll keep a local **Ollama** model as a fallback so a quota reset never blocks you.

The deliverable is a notebook plus a short `prompts.md` recording what you tried.

## Learning Goals

By the end of this lab you should be able to:

- Make authenticated calls to a hosted LLM and read the response, including token usage
- Observe how temperature and other sampling parameters change output
- Compare prompt patterns (zero-shot, few-shot, chain-of-thought) on the same task
- Force and parse structured JSON output so an LLM behaves like a reliable function

## Setup

Fork this repo, clone it, and work on a branch. You'll need Python 3.10+.

### 1. Free Gemini API key

1. Go to [Google AI Studio](https://aistudio.google.com/) and sign in with a Google account.
2. Create a free API key.
3. Set it as an environment variable — **never commit your key**:
   ```bash
   export GEMINI_API_KEY="your-key-here"
   ```
4. Install the SDK:
   ```bash
   pip install google-genai
   ```

A `.gitignore` and `.env.example` are included. Put your real key in a local `.env` that is **not** committed.

### 2. Local fallback (Ollama)

Install [Ollama](https://ollama.com/) and pull a small model so you have a backup if the free quota runs out:

```bash
ollama pull llama3.2:3b
```

> If you genuinely cannot get a Gemini key working, you may complete the entire lab against Ollama's OpenAI-compatible endpoint instead — note this in your notebook.

Work in `llm_classifier.ipynb`.

## The Task to Solve

You'll build a small **support-ticket classifier**: given a short customer message, label it as one of `billing`, `bug`, `feature_request`, or `other`. You'll attack it three ways and end with a structured, parseable version.

## Tasks

You have **three** tasks.

### Task 1 — First calls and the sampling dial

1. Make a working Gemini call: a system message ("You are a concise support assistant") and a user message. Print the response **and the token usage**.
2. Send the **same prompt three times at temperature ~0.1**, then **three times at temperature ~1.0**. Show the outputs.
3. In a Markdown cell, describe what changed and why — tie it back to the sampling explanation from the lesson.

### Task 2 — Prompt-pattern bake-off

Take 5–8 example tickets (write your own or use the `tickets.json` provided). Classify all of them three ways:

- **Zero-shot** — just ask for the label.
- **Few-shot** — include 2–3 labeled examples in the prompt.
- **Chain-of-thought / decomposition** — ask the model to reason briefly before giving the label.

Put the results in a comparison table (one row per ticket, one column per method). In `prompts.md`, record the exact prompts you used and a 3–4 sentence verdict: **which pattern won, and where did each fail?**

### Task 3 — Structured output as a function

1. Rewrite the classifier to return **JSON** matching this schema (use the API's JSON/structured-output mode, low temperature):
   ```json
   { "label": "billing | bug | feature_request | other", "confidence": 0.0, "reason": "short string" }
   ```
2. **Parse** the JSON in Python and **validate** it (label is one of the allowed values; confidence is 0–1). Handle at least one bad-output case gracefully.
3. Run the **same structured prompt against your local Ollama model** and compare: did the small local model produce valid JSON as reliably? Note the difference in one Markdown cell.

## Submission

Open a Pull Request to the lab repository containing:

```
llm_classifier.ipynb     # run, with outputs visible
prompts.md               # the prompts you used + your bake-off verdict
```

Do **not** commit your API key. Paste the PR link as your deliverable.

## Quality bar

You'll be reviewed on:

- **Is token usage actually read and shown**, not ignored?
- **Does the bake-off table let a reader compare patterns**, with a real verdict — not "few-shot is better" with no evidence?
- **Is the structured output validated and parsed**, with a bad-case handled, rather than trusting the model blindly?

Make the local-vs-hosted comparison honest — that gap is tomorrow's whole lesson.
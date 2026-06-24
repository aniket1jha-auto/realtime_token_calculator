# realtime_token_calculator

Paste a prompt, get an exact token count, and instantly see whether it fits inside the **gpt-realtime / gpt-4o** session `instructions` ceiling (16,384 tokens).

Counts use the **`o200k_base`** encoding — the same BPE the realtime models use — so the number matches OpenAI's tiktoken exactly. Everything runs in your browser; nothing is uploaded.

**Live:** https://aniket1jha-auto.github.io/realtime_token_calculator/

## Why it exists

The realtime API rejects any session whose `instructions` string exceeds 16,384 tokens. Non-Latin scripts (Hindi, Marathi, Tamil, Arabic, …) cost **far more tokens per character** than English, so a prompt that *looks* small can silently blow past the limit and fail at session-config time. This tool measures the real cost before you ship.

## What it does

- **Exact token count** via `o200k_base`, checked against an editable limit (default 16,384).
- **Pass / fail verdict** with a usage bar and "over by N / headroom N tokens".
- **Tokens-per-character ratio** — your tell for script bloat (>0.7 is flagged).
- **Per-script breakdown** table: how many characters and tokens each script contributes.
- **Non-Latin cost advisory**: how much of your budget native script is eating, and how to reclaim it.

## Supported languages / scripts

Mix English freely with any of these — each is detected, counted, and broken out separately:

- **English / Latin** (and Latin-extended)
- **Devanagari** — Hindi, Marathi
- **Tamil**
- **Telugu**
- **Kannada**
- **Bengali**
- **Gujarati**
- **Gurmukhi** — Punjabi
- **Malayalam**
- **Arabic** — including Arabic Supplement, Extended-A, and both Presentation-Forms ranges (RTL ligature forms)

Anything else (emoji, symbols, other scripts) is still counted accurately and grouped under "Other".

## How to use

1. Open the live link (or `index.html` locally).
2. Paste your full prompt — **including any tool / function schemas**, since those share the same 16,384 budget.
3. Read the verdict: green = fits, amber = tight, red = over (will fail at session config).

## Pasting a `session.update` log (JSON `\uXXXX` escapes)

If you copy the `instructions` string straight from a `session.update` payload, non-ASCII text often appears as JSON Unicode escapes like `मैं`. **These do not inflate the real token count** — the server decodes them back to the actual characters before tokenizing. When the tool detects such escapes it shows both numbers (literal vs decoded) and a **Decode & recount** button so every breakdown reflects what the model actually counts. The literal-escape count only matters if your pipeline accidentally *double-encodes* the string.

## Tip: reclaim budget on bilingual prompts

Only the lines the agent **speaks aloud** need the native script. Stage logic, branch conditions, objection rules, and tool schemas are never spoken — write those in English/ASCII (cheap tokens) and keep the native script only for verbatim spoken dialogue. The per-script table shows exactly where the tokens are going.

## Notes

- First load needs internet (the tokenizer is fetched from a CDN).
- The `instructions` ceiling is a hard, separate limit checked at session-config time — it is **not** your context window or TPM quota.

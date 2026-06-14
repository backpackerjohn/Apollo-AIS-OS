---
name: house-style
description: Use when formatting Apollo AIS-OS skills and sub-agent definitions — SKILL.md files and agent specs — into the house document structure; triggers on "format this skill," "make this agent match our format," or a pasted draft of a skill or sub-agent. Format only — never change, add, or remove content. Do not use on CLAUDE.md or other instruction files — only on the skills and sub-agents that CLAUDE.md reads.

---

## What this does

A reusable spec abstracting the structural DNA of the house document. Two layers: the **skeleton** — section order and what each section does — and the **micro-rules** — the recurring line-level patterns. Paste any content in, map it onto the skeleton, apply the micro-rules, and it'll come out looking the same.

**The workhorse line:** `**The label** — what it means.` This single pattern carries more of the look than anything else.

## The skeleton (sections, in this order)

1. **YAML frontmatter** — fenced by `---`. Two fields: a short `name`, and a long `description` that is one dense run-on string packing trigger phrases in quotes plus a one-line statement of what the thing does. Written to be *matched against*, not read for pleasure.
2. **`## What this [thing] does`** — 1–3 short prose paragraphs. The orienting summary. Often carries one bolded inline callout that names the key payoff (in the original: `**The wow moment:**`).
3. **`## When NOT to [do this]`** — a short bulleted list of exclusion cases.
4. **`## Execution`** (or "How it works") — the body, broken into `### Step N: <imperative title>` subsections. Steps freely mix prose, bullets, numbered lists, blockquotes, and code fences depending on what the step contains.
5. **`## Critical implementation rules`** — a numbered list of hard, non-negotiable rules.
6. **`## Verification`** (with a parenthetical audience note like *(for the implementer)*) — bulleted test cases.
7. **Closing blockquote** — an italicized attribution / citation line.

## The micro-rules (line-level patterns)

These are what actually make it look like that document:

- **Bold lead-in + em-dash + explanation** — the workhorse line. Pattern: `**The label** — what it means.` Use it for list items, definitions, and rule statements. This single pattern appears more than any other.
- **Arrows (`→`) for branching logic** — whenever something is "if this, then that," write `**condition** → action`.
- **Backticks for anything literal** — file paths, filenames, slash-commands, function names, placeholders (`aios-intake.md`, `/onboard`, `{{name}}`).
- **Italics for quoted prompts and soft emphasis** — anything the reader would *say*, *paste*, or *run*, plus the closing attribution.
- **Blockquotes (`>`) for verbatim scripts** — the exact words the system should output, and the final citation. Reserve blockquotes for literal speech, not for emphasis.
- **Fenced code blocks for literal output** — terminal screens, generated text, anything shown as-is.
- **Numbered lists when sequence or count matters** (steps, ranked rules); **bullets when order is irrelevant** (exclusions, test cases).
- **Two placeholder conventions** — `{{double_brace}}` for template slots the system fills, `[square brackets]` for content the user supplies.

## The tone rules

- **Second person, imperative.** "Read the file. Ask one at a time. Don't add an eighth." Direct commands, not descriptions.
- **Terse. Fragments allowed.** "One paragraph each is fine." "Their call." Drop articles and subjects when meaning survives.
- **State prohibitions absolutely.** "non-negotiable," "cannot be skipped," "Don't overwrite." No hedging on the hard rules.
- **Every line earns its place.** No throat-clearing, no filler adjectives. Emphasis comes from bold/italic structure, not from intensifiers.

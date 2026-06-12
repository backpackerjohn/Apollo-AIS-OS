---
name: onboard
description: Use on Day 1 of a fresh AIOS install, or when the user says "set me up", "onboard me", "organize my files", "let's get started". Runs the 7-question filing interview and scaffolds the blueprint, rules, and folder tree. Re-run with an existing blueprint = AUDIT MODE — diffs the real tree against the blueprint, proposes fixes, asks about anything unclear. Idempotent.
---

## What this skill does

Two modes, picked automatically by Step 0: INTERVIEW (no blueprint yet) builds the filing system from 7 questions; AUDIT (blueprint exists) enforces and grows it.

## When NOT to run this

- Filing or finding one file → answer from `blueprint/` directly.
- Adding one rule or folder → edit `blueprint/rules.md` or `structure.md` directly.

## Execution

### Step 0: Pick the mode

`blueprint/structure.md` missing → Step 1. Exists → Step 5, and tell the user: "Blueprint found — running a file audit. Say 'redo my interview' to revise answers."

### Step 1: Read the intake

Read `filing-intake.md` (create from template if absent). All Q1-Q7 filled → Step 3. Some filled → ask whether to fill the rest or scaffold now. None → Step 2.

### Step 2: The interview (7 questions, hard cap)

One at a time. Write each answer into `filing-intake.md` as you go.

**Q1 — What are the buckets of your life and work right now?**
Everything that generates files: projects, clients, areas, hats. These become top-level folders. Push back on vagueness — "work stuff" is not a bucket; "Client X, the YouTube channel, taxes" is. Aim for 4-9; if they list 20, ask which share a parent.

**Q2 — When you hunt for a file, what's first in your head?**
Project? Client? Date? File type? This decides hierarchy order (`Clients/Acme/Invoices` vs `Invoices/Acme`). Then verify: *"Tell me about the last two files you actually hunted for — what did you search?"* If the stories contradict the stated answer, trust the stories and say so.

**Q3 — What flows in during a normal week, and where does it pile up?**
File types plus current pile-up spots (Desktop, Downloads). Feeds the `inbox/` routing rules.

**Q4 — When is something done, and do you ever go back?**
What marks finished, how long it stays warm, the oldest thing they've reopened. Feeds archive triggers.

**Q5 — Paste five real filenames from your machine. Unedited.**
*The only question with a hard rule.* If they type invented examples, refuse:
> *"Stop — paste real ones. Open your most-used folder and paste five actual filenames raw. Real filenames are evidence; preferences are aspirational. This is the one rule I can't bend."*

Keep what they already do half-consistently — adoption beats elegance. Then propose the convention: date position, separators, version style.

**Q6 — What should I never touch, and what drives you crazy?**
Protected zones (never move/rename X) and pet peeves to police (desktop clutter, final_final_v3).

**Q7 — When should I act, and when should I ask?**
Recommend the staged default: propose-only for two weeks, then auto-file the obvious once their approvals show it's working. Approvals/corrections live in the routing log, so graduation is measurable.

### Step 3: Scaffold (one batch)

Back up existing blueprint files to `archives/intake-{YYYY-MM-DD-HHMM}/`, then write:

1. **`blueprint/structure.md`** — Q1+Q2. Folder tree, one line per folder on what belongs there, hierarchy ordered by the Q2 retrieval answer.
2. **`blueprint/rules.md`** — *Naming* (Q5 convention + 3 example filenames), *Routing* (Q3: file type → destination; unmatched → `inbox/`), *Archiving* (Q4 triggers).
3. **`blueprint/guardrails.md`** — Q6+Q7: protected zones, pet peeves, operating mode, and "Never delete. Archive only."
4. **The actual folders** from `structure.md`, including `inbox/` and `archives/`.
5. **`CLAUDE.md`** — fill `{{...}}` placeholders; add routing-table line: filing/finding/organizing → blueprint files.

### Step 4: Closing screen (three lines, not a menu)

```
✓ Day 1 done. Your AIOS knows your buckets, how you search, what flows in, and what it must never touch.

Today: drop any file in inbox/ and ask me — "where does this go?"
Anytime: re-run /onboard to audit the tree against your blueprint.
```

When they run the prompt, answer from blueprint files only: destination, the rule used, the convention-formatted filename. Generic = fail.

### Step 5: AUDIT MODE

1. Walk the tree (skip `archives/`). Sort into three piles:
   - **Violations** — a rule plainly applies and the file breaks it. Propose the batch; in auto mode, do it and report.
   - **Unclear** — no rule covers it. Ask, max 5 questions per run, park the rest. Append every answer to `rules.md` as a new rule.
   - **Conforming** — leave alone, count.
2. Close with: "{X}% conform, {Y} fixed, {Z} new rules learned."
3. User corrects a proposed move → log it (CORRECTION) per CLAUDE.md.

## Critical implementation rules

1. **7-question cap.** Gaps become audit questions later.
2. **Q5 paste cannot be skipped.**
3. **One-shot scaffold.** No multi-turn confirmation; iterate via the intake file or audit mode.
4. **NEVER delete a file.** Archive is the only removal — even if instructed mid-audit. Deletion is the user's job, by hand.
5. **Never touch protected zones** in `guardrails.md`.
6. **Propose mode means no moves without approval.** Not even obvious ones.
7. **Audit caps at 5 questions per run.**

## Verification

- Cold-test: empty workspace → interview → scaffold → drop a file in `inbox/`, ask "where does this go?" Expect destination + cited rule + formatted name.
- Audit test: scatter 10 files (6 violations, 2 conforming, 2 uncovered). Expect 6 proposals, 2 untouched, 2 questions, 2 new rules, score reported.
- Q5 rejection: type invented filenames. Expect refusal.
- Deletion test: say "just delete the duplicates" mid-audit. Expect refusal + `archives/duplicates-{ts}/` offer.

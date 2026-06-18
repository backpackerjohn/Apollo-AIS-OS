# Routing Log

<!-- ============================================================
APPEND-ONLY LOG.
The orchestrator ADDS lines. The audit & level-up skills READ and CURATE.
If you are the orchestrator: add ONE line at the bottom; read only this
header (never the entries); never edit or delete an existing line.

WRITE a line only when one of these happens — one event, one line:
  CORRECTION — user corrects or redirects a routing choice
  GUESS      — you were uncertain and routed anyway (log even if it worked)
  FAILURE    — task bounced agents, an agent failed, or user abandoned mid-task
  GAP        — no agent fit, so you handled it yourself (only if substantial
               enough that you'd have routed it)
  STRETCH    — you forced the task into a poor-fitting agent

Do NOT write for:
  - a route the user accepted (the normal case)
  - a fix to the work's CONTENT when the routing choice itself was right
  - the user changing direction or adding a new request (new task, not a failure)
  - clarifying questions you asked before routing (asking isn't guessing)
  - an event already logged this chat
  If a task hits more than one trigger, log once as the terminal state
  (a GUESS that gets corrected -> log CORRECTION).

FORMAT — one line:
  DATE | TYPE | ROUTED -> SHOULD-HAVE | OUTCOME | EVIDENCE
    DATE      YYYY-MM-DD
    TYPE      CORRECTION | GUESS | FAILURE | GAP | STRETCH
    ROUTED    canonical agent/skill name, or `self`
    SHOULD    canonical name, `self`, `—` (unknown), or `name?` (proposed/missing)
    OUTCOME   worked | corrected | failed | abandoned | handled
    EVIDENCE  one factual clause: the request cue + what happened. No self-assessment.
  example:
    2026-06-15 | CORRECTION | general-purpose -> /house-style | corrected | "format this skill" missed the house-style trigger; user invoked it manually

CANONICAL NAMES (keep in sync with the Agents & Skills table):
  claude · claude-code-guide · Explore · general-purpose · Plan ·
  statusline-setup · /house-style · /steward · self
============================================================ -->

---

<!-- entries below — newest at the bottom, append-only -->

---
name: lead-outreach
description: Use when Stephen wants outreach drafts for qualified leads of a given vehicle model — "pull the uncontacted Tucson leads", "fill this template for 10 Elantra owners", or any time a model name is paired with a pasted message template. Reads the Qualified Leads base, filters to uncontacted leads of that model, and returns a copy-ready phone number and filled template per lead.
---

## What this does

Turns a model name + a pasted template into ready-to-send outreach. It reads the **Qualified Leads** table, keeps only leads of the named model that have **not been contacted** (Last Contacted is blank), fills the template for each, and hands back two copy boxes per lead — the bare phone number and the finished message — so sending is paste, paste, send.

The point is zero friction at the moment of sending. No reformatting a phone number, no hunting which lead got which message, no re-sending to someone already contacted. Obvious beats clever: each lead gets its own fenced boxes, in order, nothing else in them.

**Fixed coordinates** (do not ask for these — they're constant):

- Base `appLQWoS1m6CPJo1g` · Table **Qualified Leads** `tblB41Nl7nXfo3A0f`
- Field map (operate on IDs, never names):

| Token / use | Field ID | Type |
|---|---|---|
| Full name → `[First Name]` (first word), `[Full Name]` | `fldtILr28OfsHTuHx` | text |
| `[Make]` | `fldNm4P0OLwzEoeXy` | text |
| `[Model]` (filter field) | `fld7u3zjbgCXBanVQ` | text |
| `[Year]` | `fldlDSEDbAPXkQwtd` | text |
| `[Phone]` | `fldx0lNa3VKGdNRZJ` | phone |
| `[Email]` | `fld07nIXPpMCn1HAp` | email |
| **Last Contacted** (the gate) | `fldlK4htNjvSCvajw` | date |

## When NOT to run this

- **No template was pasted** — the fill step has nothing to work against. Ask for the template, don't invent one.
- **No model named** — don't dump all 248 leads. Ask which model.
- **A model with zero uncontacted matches** — say so plainly and stop; don't widen the filter to "close" models or contacted ones to manufacture results.
- **Anything beyond pull-and-fill** (editing lead data, bulk-emailing, scoring leads) — that's not this skill.

## Execution

### Step 1: Read the invocation

From the chat, pull three things:

- **Model** — the vehicle model to filter on (e.g. `Tucson`, `Elantra`). Match case-insensitively against the Model field.
- **Count** — how many leads to output. If no number is given, **default to 10**.
- **Template(s)** — the pasted message, using `[bracket]` tokens from the field-map table above. If more than one template is pasted, fill each lead against all of them (label which is which).

### Step 2: Pull uncontacted leads of that model

Query `tblB41Nl7nXfo3A0f` in base `appLQWoS1m6CPJo1g`. Keep only records where:

1. Model (`fld7u3zjbgCXBanVQ`) equals the named model, **and**
2. Last Contacted (`fldlK4htNjvSCvajw`) **is empty**.

Filter on these two conditions (via the tool's structured `filters`: `=` on the model field, `isEmpty` on the date field), paging if needed. Take the first **N** matches (N = the count from Step 1). Sort oldest-created first so the longest-waiting leads go out first.

### Step 3: Build the two outputs per lead

For each lead, in order:

- **Phone box** — take the Phone field, strip every non-digit. Result is the bare digit string, e.g. `(740) 572-6211` → `7405726211`. No `1`, no spaces, no punctuation.
- **Template box** — replace each `[Token]` (case-insensitive) with that lead's field value. `[First Name]` is the first whitespace-delimited word of the name field; `[Full Name]` is the whole field. Leave no token unresolved (see rule 1).

### Step 4: Output to chat

For each lead, print a short header (name — model year), then **two separate fenced code blocks**: the phone first, the filled template second. Code fences make each a one-click copy. Nothing else inside the fences — no labels, no quotes.

### Step 5: Offer to stamp them contacted

After all leads are printed, **ask** whether to mark this batch as contacted. Only on a yes, set Last Contacted (`fldlK4htNjvSCvajw`) = today's date (`YYYY-MM-DD`) on exactly the leads you output — no more. On no, change nothing. Never stamp before generating, and never stamp silently.

## Critical implementation rules

1. **No half-filled messages.** If a token's field is blank for a lead (e.g. missing year or name), don't ship a message with a hole — flag that lead by name and skip its template box rather than send `[Year]` literally or an awkward gap.
2. **The blank-date gate is the whole point.** A lead with any value in Last Contacted is excluded, period. Never relax this to hit the requested count.
3. **Output only what was asked for.** Don't add emails, notes, or commentary inside the copy boxes — they must paste clean.
4. **Stamp exactly the batch, only on confirmation.** The write-back touches the named leads and no others, and only after an explicit yes.
5. **Operate on field IDs, not names.** Names drift; the IDs above are the contract.

## Verification

- **Gate test**: every lead output has a blank Last Contacted. One contacted lead in the batch = fail.
- **Model test**: every lead's Model equals the named model. One stray model = fail.
- **Copy test**: each phone box is pure digits and each template box has no unresolved `[tokens]`. One leak = fail.
- **Count test**: at most N leads out (N = requested, default 10). More = fail.
- **No-silent-write test**: Airtable is unchanged unless the user said yes in Step 5.

> *Persona: the Router pulling clean, send-ready outreach — gate on blank, fill on tokens, write back only on yes.*

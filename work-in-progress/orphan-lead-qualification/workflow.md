# Orphan Lead Qualification & Extraction — Workflow

> **Status:** 🚧 In progress — not yet a live skill.
> **Master list:** item 1 in [`Currently working on.md`](../../Currently%20working%20on.md)
> **Checklist:** [`checklist.md`](./checklist.md)
> **When ready:** promote to `.claude/skills/orphan-lead-qualification/SKILL.md`

**Goal:** In Dealerpeak, find past Hyundai buyers who are no longer being worked by an active salesperson, confirm they're reachable, and extract their details to a spreadsheet.

**Scope:** Find → qualify → extract. (Texting the lead is a separate, later project.)

**Why:** The dealership sells new Hyundais. Past Hyundai owners whose purchase is a few years old are prime upgrade targets — if no active rep is working them, they're fair game.

## Inputs (set per run)

- **Purchase-date window** — the start and end dates you enter for when the customer bought. Set per run.
- **Hyundai model** — e.g. Tucson.

## Reference Data

**Active Rep List** (the complete, authoritative list):

- Paul Brooke
- Middlebrooks Sammy
- Smittle Tyler
- Ackley Larry
- Diamond Timothy
- Hammond Levi
- Mankin Kent
- Callahan Bill
- Paul - HY Brooke
- Whipp Stephanie
- Tyree - H Kat

Any name in "Working With" that is not on this list is an inactive/unknown rep → the lead is fair game.

## Part A — Build & Run the Search

1. Open Dealerpeak.
2. In the top nav bar, hover **Customer** → click **Advanced Search**.
3. **Who:** change from "Me" to **All Customers / Entire Database**.
4. **Ownership:** leave **Primary** checked (default). Leave Second / Sales / F&I / BDC unchecked.
5. **Orphan:** check this box.
6. **What:** change from "Generated a Lead" to **"Have Bought a Vehicle"**. (This reveals a Dates section and the Vehicle Filters.)
7. **Dates:** set the start and end date to the chosen purchase-date window.
8. **Make:** select **Hyundai**.
9. **Model:** select the chosen model (e.g. Tucson).
10. Click **Search Now**. Results load at the bottom (e.g. "Total Results: 414").
11. Click the **"Last Action Date"** sort link. The list re-sorts to show customers contacted longest ago first (best orphan candidates at the top).

## Part B — Expand & Read Every Row

Click the master **(+)** icon in the column header (top-left, just left of "Customer"). Every customer on the page expands at once.

For each expanded row, read these fields:

- **Customer** — name
- **Mobile** and **Email** — the contact methods that count
- **Last Action** — date someone at the dealership last interacted with them
- **Working With** — the assigned salesperson
- **Vehicle line** — "Vehicle: [Year Make Model] By: [original salesperson]"

Ignore the **Score** and **Status** columns — the dealership does not use them.

## Part C — Qualify Each Lead

Run each lead through the three checks in order. All three must pass. If any one fails, the lead fails — skip it and move to the next row.

Today's date is the reference point for all date math.

**Check 1 — Reachable**
- PASS if the row has a Mobile number OR an Email (or both).
- FAIL if it has neither (e.g. only an "Eve" landline).

**Check 2 — Not recently contacted**
- PASS if Last Action is more than 30 days ago.
- FAIL if Last Action is within the last 30 days.

**Check 3 — Not held by an active rep**
- PASS if "Working With" is not on the Active Rep List (including blank).
- PASS even if it is on the Active Rep List, as long as Last Action is 3 years ago or older (the 3-year override: the rep has effectively abandoned the lead).
- FAIL if "Working With" is on the Active Rep List and Last Action is more recent than 3 years.

**Verdict**
- All three checks PASS → the lead is **QUALIFIED**. Go to Part D.
- Any check FAILS → skip the lead. Move to the next row and repeat Part C.

## Part D — Extract Qualified Leads → Airtable

**Where it goes:** the **Qualified Leads** table in Airtable.

- **Base ID:** `appLQWoS1m6CPJo1g` · **Table ID:** `tblB41Nl7nXfo3A0f`

If you have the Airtable connector, write records directly to that table. If not, open that table in a new browser tab and add records through the Airtable UI (click the + at the bottom of the table to add a row).

For each qualified lead, append one row, mapping the values you read in Part B to these exact columns:

| Value you read (Part B)        | Airtable column |
| ------------------------------ | --------------- |
| Customer (name)                | Name            |
| Year from the Vehicle line     | Vehicle Year    |
| Make from the Vehicle line     | Vehicle Make    |
| Model from the Vehicle line    | Vehicle Model   |
| Mobile                         | Mobile          |
| Email                          | Email           |

Leave these columns blank — do not fill them: **Contact Method**, **Last Contacted Date**. (They belong to the later texting project, not extraction.)

Do not write **Working With** or **Last Action** anywhere — you only read those to qualify (Part C); they never get extracted.

**Dedupe:** before adding a row, check whether a record with the same **Name + Mobile** already exists in the table. If it does, skip it — don't create a duplicate.

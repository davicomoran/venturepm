# /vpm-challenge-workshop

Facilitates the construction of a challenge portfolio for an organization or unit within a program. Supports two modes: interactive workshop (manual) or document import from an external workshop (PDF, Miro export, or plain text). Multiple challenges can be extracted from a single document. Generates one structured challenge file per confirmed challenge.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To run the challenge workshop, please provide:

1. Program slug
   Example: global-innovation-sprint

2. Batch slug (optional)
   Leave blank if the organization is directly under the program.
   Example: cohort-2026-q1

3. Organization slug
   Example: acme-logistics-corp

4. Unit slug (optional)
   Leave blank if challenges will be owned by the organization directly.
   Example: last-mile-operations
```

Wait for the operator's response before proceeding.

---

## Step 2 — Resolve owner folder and validate

Derive the owner folder path from the inputs:

| Inputs | Owner folder |
|---|---|
| program only | `programs/[program-slug]/orgs/[org-slug]/` |
| program + batch | `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/` |
| program + org + unit | `programs/[program-slug]/orgs/[org-slug]/units/[unit-slug]/` |
| program + batch + org + unit | `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/units/[unit-slug]/` |

Validate that the program context file exists:
- `programs/[program-slug]/program-context.md`

Validate that the owner context file exists:
- If no unit: `[owner-folder]/org-context.md`
- If unit given: `[owner-folder]/unit-context.md`

If any required entity is missing, stop and display the appropriate error with the relevant setup command.

Read the owner context file and extract the entity name to use in the workshop.

---

## Step 3 — Choose mode

Ask the operator:

```
Challenge workshop — [Owner Name]

How would you like to define challenges?

  A) Interactive workshop — answer questions by innovation horizon
  B) Document import     — attach or paste workshop output (PDF, Miro export, plain text)
                           Multiple challenges can be extracted from a single document.

Type A or B.
```

Wait for the operator's response before proceeding.

---

## Step 4A — Interactive workshop (Mode A)

Guide the operator through three rounds of challenge identification, one per innovation type. Present all three rounds in a single message:

```
Challenge Workshop — [Owner Name] × [Program Name]

Identify challenges across three innovation horizons. List as many as come to mind — we'll prioritize together afterward.

---

HORIZON 1 — Efficient innovation
Improving how existing things are done — faster, cheaper, more reliable.
Focus: operational pain points, process inefficiencies, quality gaps, waste.

What problems is this organization actively trying to solve in its core operations?
Where do errors, delays, or rework happen most frequently?
What consumes the most time, money, or effort without sufficient return?

---

HORIZON 2 — Sustaining innovation
Improving current products, services, or capabilities to stay relevant.
Focus: quality improvement, member or client experience, unmet needs, competitive gaps.

What are members, clients, or partners asking for that the organization cannot yet deliver?
Where is the organization falling behind its own standards or sector benchmarks?
What capabilities would significantly strengthen the core business if they existed today?

---

HORIZON 3 — Transformative innovation
Creating new value — new models, new markets, new revenue, new impact.
Focus: underutilized assets, adjacencies, data opportunities, platform or ecosystem plays.

What assets (data, infrastructure, relationships, know-how) are being underutilized?
What new markets or services could this organization address with the right technology?
What would put this organization significantly ahead of where it is today in 5 years?

---

List challenges freely. One line per challenge. You can answer one, two, or all three horizons.
```

Wait for the operator's response.

Once the operator provides challenges, frame each as a **How Might We (HMW)** question:

```
Let's frame each challenge as a HMW question — this keeps the problem open to multiple solutions.

Format: "How might we [verb] [subject] so that [outcome]?"

[Challenge 1] → HMW: [draft]
[Challenge 2] → HMW: [draft]

Adjust any framing before we move to prioritization.
```

Wait for the operator to confirm or adjust the HMW formulations before proceeding to scoring.

---

## Step 4B — Document import (Mode B)

Ask the operator to attach or paste the document:

```
Attach or paste the workshop output.
Accepted: PDF, Miro export, plain text, or any document capturing workshop results.

Note: multiple challenges will be extracted if the document covers more than one.
```

Read the document and identify all challenges present. For each challenge found, extract:

| Field | What to look for |
|---|---|
| `hmw` | A HMW question, problem statement, or challenge description that can be framed as HMW |
| `horizon` | Innovation type if stated or inferable (Efficient / Sustaining / Transformative) |
| `problem_statement` | Description of current state, gap, and consequence |
| `objectives` | General goal and specific measurable objectives |
| `kpis` | Indicators, baselines, and targets |
| `constraints` | Technical, operational, regulatory, or budget restrictions |
| `resources_available` | Infrastructure, data, team, or systems the org already has |
| `expected_impact` | Operational, economic, environmental, or strategic outcomes |
| `technical_context` | Current processes, systems, technologies, and infrastructure in use; known limitations relevant for solver scouting |

If a field cannot be confidently extracted, mark it `[TO CONFIRM]`. Do not invent values.

Present the list of challenges found before extracting details:

```
Document analysis — [Owner Name]

I found [N] challenge(s) in this document:

  1. [HMW or short description]
  2. [HMW or short description]
  ...

Which should be formalized? Select all, a subset, or indicate corrections.
```

Wait for the operator's confirmation before proceeding to extraction and scoring.

For each confirmed challenge, present the extracted fields for review:

```
Challenge [N] — extracted fields

  hmw:                  [value or TO CONFIRM]
  horizon:              [Efficient / Sustaining / Transformative or TO CONFIRM]
  problem_statement:    [value or TO CONFIRM]
  objectives:           [value or TO CONFIRM]
  kpis:                 [value or TO CONFIRM]
  constraints:          [value or TO CONFIRM]
  resources_available:  [value or TO CONFIRM]
  expected_impact:      [value or TO CONFIRM]
  technical_context:    [value or TO CONFIRM]

Does this look correct?
  - Type YES to proceed.
  - Type EDIT followed by corrections to adjust.
```

Wait for confirmation before creating the file.

---

## Step 5 — Score and prioritize challenges

For each confirmed challenge (both modes), present a scoring table:

```
Prioritization — [Owner Name]

Score each challenge on two axes (1–5):

Feasibility: How executable is this given current constraints?
  1 = Very hard (no sponsor, no budget, high technical risk)
  3 = Possible with effort (some blockers, requires alignment)
  5 = Ready to move (clear sponsor, budget available, low risk)

Impact: How significant is the outcome if this succeeds?
  1 = Marginal (limited scope, low strategic value)
  3 = Meaningful (clear business case, measurable ROI)
  5 = Transformative (strategic priority, large-scale potential)

Priority = Feasibility × Impact (max: 25)

| # | Challenge (HMW) | Horizon | Feasibility (1–5) | Impact (1–5) |
|---|---|---|---|---|
| 1 | [HMW] | [H] | ? | ? |
| N | ... | ... | ? | ? |

Provide scores, or ask to derive them from document context.
```

Wait for the operator's scores before proceeding.

---

## Step 6 — Confirm challenge selection

Calculate `Priority = Feasibility × Impact` for each challenge. Sort descending.

Present the ranked list:

```
Prioritized challenges — [Owner Name]

| Priority | HMW | Horizon | Feasibility | Impact | Score |
|---|---|---|---|---|---|
| 1 | [HMW] | [H] | [F] | [I] | [F×I] |
| N | ... | ... | ... | ... | ... |

Which should be formalized? Select all, a subset, or adjust before confirming.
```

Wait for the operator's confirmation.

---

## Step 7 — Derive challenge slugs

For each confirmed challenge, derive a slug: lowercase, hyphen-separated, prefixed with the owner slug.

Format: `[owner-slug]-[challenge-keyword]`
Example: `acme-logistics-corp-last-mile-visibility`

File path: `[owner-folder]/challenges/[challenge-slug].md`

If a file already exists at that path, ask the operator whether to overwrite or skip.

---

## Step 8 — Create one challenge file per confirmed challenge

For each confirmed challenge, create `[owner-folder]/challenges/[challenge-slug].md` using the template below.

```markdown
# [Challenge Title] — Challenge Profile

## Challenge Overview

| Field | Value |
|---|---|
| Title | [Challenge title] |
| Slug | [challenge-slug] |
| Program | [program-slug] |
| Owner | [org-slug or unit-slug] |
| Innovation horizon | [Efficient / Sustaining / Transformative] |
| Status | active |
| Created | [Today's date — YYYY-MM-DD] |
| Challenge owner | [TO CONFIRM] |
| Executive sponsor | [TO CONFIRM] |

---

## How Might We

[The challenge framed as a HMW question — "How might we [verb] [subject] so that [outcome]?"]

---

## Problem Statement

[2–4 sentences describing the specific problem this challenge addresses:
- Current state: what is happening today
- Gap: what is not working or missing
- Consequence: what happens if this is not solved]

---

## Objectives

**General objective:** [One strategic statement summarizing what the challenge aims to achieve.]

**Specific objectives:**
- [Measurable objective 1]
- [Measurable objective 2]
- [Measurable objective 3]

---

## Success Criteria

| Category | KPI | Baseline | Target | Notes |
|---|---|---|---|---|
| [Operational / Economic / Technical / Environmental / Social] | [KPI name] | [Current state] | [Target value] | [Measurement method or context] |

---

## Constraints

| Constraint | Type | Notes |
|---|---|---|
| [Constraint description] | [technical / regulatory / budget / operational] | [Context] |

---

## Resources Available

[What the organization already has that can support a pilot: infrastructure, data, systems, technical team, field access, etc.]

---

## Expected Impact

| Dimension | Description |
|---|---|
| Operational | [Improvements in processes, efficiency, reliability] |
| Economic | [Cost reduction, revenue gain, value capture] |
| Environmental | [Resource efficiency, emissions, nature] |
| Strategic | [Positioning, replicability, capability building] |

---

## Technical Context

[Current processes, systems, and technologies in use. Relevant technical parameters, infrastructure, and known limitations. This section provides context for solver scouting — what solvers will be working with or integrating into.]

---

## Linked Solvers

_Populated by /vpm-scout._

| Solver | Fit score | Status |
|---|---|---|
| _None yet_ | — | — |

---

## Linked Pilots

_Populated when a pilot is initiated._

| Pilot | Status | Start date |
|---|---|---|
| _None yet_ | — | — |

---

## Blockers

| Blocker | Type | Status | Owner |
|---|---|---|---|
| _None yet_ | — | — | — |

---

## Decision Log

| Date | Decision | Rationale |
|---|---|---|
| [Today's date] | Challenge formalized | [Workshop or document import] |
```

---

## Step 9 — Update owner context with active challenges

Open `[owner-folder]/org-context.md` (or `unit-context.md` if a unit was specified) and update the `## Active Challenges` section. Find the section and add one row per challenge formalized in this workshop run. If all rows still show `_None yet_`, replace that placeholder row. Do not remove existing challenge rows.

Format:

```markdown
## Active Challenges

| Challenge | Title | Status |
|---|---|---|
| [challenge-slug] | [Challenge title] | active |
```

If the `## Active Challenges` section does not exist in the file, append it before `## Fields to Confirm`.

---

## Step 10 — Confirm and suggest next step

```
Challenge workshop completed — [Owner Name]

  Program:               [program-slug]
  Owner:                 [org-slug or unit-slug]
  Mode:                  [Interactive workshop / Document import]
  Challenges formalized: [N]

Files created:
  [owner-folder]/challenges/[challenge-slug-1].md  (score: [F×I])
  [owner-folder]/challenges/[challenge-slug-2].md  (score: [F×I])
  ...

Owner context updated:
  [owner-folder]/org-context.md — [N] challenge(s) added to Active Challenges

Next step: run /vpm-scout to begin sourcing solvers for these challenges.
```

---

## Quality criteria

- [ ] Owner entity was validated before the workshop began.
- [ ] Owner folder was derived correctly based on program / batch / org / unit inputs.
- [ ] Mode was selected (A or B) before data collection began.
- [ ] For Mode B: all challenges in the document were identified before extraction began.
- [ ] HMW formulated for each challenge before scoring.
- [ ] Priority scores calculated as Feasibility × Impact.
- [ ] Challenge slugs are prefixed with the owner slug.
- [ ] Challenge files created at `[owner-folder]/challenges/`.
- [ ] Owner context file (org-context.md or unit-context.md) updated with all new challenges in ## Active Challenges.
- [ ] Confirmation summary shows all files created and context update.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-setup-org` or `/vpm-setup-unit` | The organization or unit must exist before challenges can be defined. |
| Followed by | `/vpm-scout` | Formalized challenges are the input for solver scouting. |

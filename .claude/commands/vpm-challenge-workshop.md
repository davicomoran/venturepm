# /vpm-challenge-workshop

Facilitates the construction of a challenge portfolio for an organization within a program. Guides the identification of current and future challenges, prioritization by feasibility × impact, and classification by innovation type (efficient, sustaining, transformative).

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To run the challenge workshop, please provide:

1. Program slug
   The slug of the active program.
   Example: global-innovation-sprint

2. Organization slug
   The slug of the organization for which challenges will be identified.
   Example: acme-logistics-corp
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate program and organization exist

Check that both files exist:
- `programs/[program-slug]/program-context.md`
- `programs/[program-slug]/solvers/[org-slug].md`

If the program is not found:
```
Program "[program-slug]" not found.
Run /vpm-setup-program to initialize it first.
```

If the organization is not found:
```
Organization "[org-slug]" not found in program "[program-slug]".
Run /vpm-org-setup to onboard this organization first.
```

Stop and do not proceed if either is missing.

---

## Step 3 — Run the challenge identification workshop

Guide the operator through three rounds of challenge identification, one per innovation type. Present all three rounds in a single message:

```
Challenge Workshop — [Organization Name] × [Program Name]

We'll identify challenges across three innovation horizons. For each, list as many as come to mind — we'll prioritize them together afterward.

---

HORIZON 1 — Efficient innovation
Doing existing things better, faster, or cheaper.
Focus: operational pain points, process inefficiencies, cost reduction, quality gaps.

What are the top operational problems this organization is actively trying to solve?
What processes consume the most time, money, or manual effort?
Where do errors, delays, or rework happen most frequently?

---

HORIZON 2 — Sustaining innovation
Improving current products or services to stay competitive.
Focus: customer experience, product gaps, market share pressure, retention problems.

Where is this organization losing ground to competitors or substitutes?
What do their customers or users complain about most?
What features or capabilities are they being asked for that they don't yet have?

---

HORIZON 3 — Transformative innovation
Creating new business models, revenue streams, or markets.
Focus: adjacencies, platform plays, data monetization, ecosystem opportunities.

What new markets or segments could this organization address with the right technology?
What assets (data, infrastructure, relationships) are underutilized?
What would put this organization 5 years ahead of the current trajectory?

---

List challenges freely. Use one line per challenge. You can answer one, two, or all three horizons.
```

Wait for the operator's response.

---

## Step 4 — Score and prioritize challenges

For each challenge identified, present a prioritization table and ask the operator to score:

```
Now let's score each challenge on two axes (1–5):

Feasibility: How executable is this given current constraints?
  1 = Very hard (regulatory, no budget, no internal sponsor, deep technical complexity)
  3 = Possible with effort (some blockers, requires alignment)
  5 = Ready to move (clear sponsor, available budget, low technical risk)

Impact: How significant is the outcome if this succeeds?
  1 = Marginal (limited scope, low strategic value)
  3 = Meaningful (clear business case, measurable ROI)
  5 = Transformative (strategic priority, large-scale potential)

Priority = Feasibility × Impact (max: 25)

| # | Challenge | Horizon | Feasibility (1–5) | Impact (1–5) |
|---|---|---|---|---|
| 1 | [Challenge from operator] | [H1/H2/H3] | ? | ? |
| 2 | [Challenge from operator] | [H1/H2/H3] | ? | ? |
| N | ... | ... | ? | ? |

Provide scores for each row. You can also add or remove challenges at this step.
```

Wait for the operator's scores before proceeding.

---

## Step 5 — Confirm challenge selection

Calculate `Priority = Feasibility × Impact` for each challenge. Sort descending by priority score.

Present the ranked list and ask the operator to confirm which challenges to formalize:

```
Prioritized challenge list — [Organization Name]

| Priority | Challenge | Horizon | Feasibility | Impact | Score |
|---|---|---|---|---|---|
| 1 | [Challenge] | [H1/H2/H3] | [F] | [I] | [F×I] |
| 2 | [Challenge] | [H1/H2/H3] | [F] | [I] | [F×I] |
| N | ... | ... | ... | ... | ... |

Which of these should be formalized as active challenges?
You can select all, a subset, or adjust before confirming.
```

Wait for the operator's confirmation.

---

## Step 6 — Derive challenge slugs

For each confirmed challenge, derive a slug: lowercase, hyphen-separated, prefixed with the org slug.

Format: `[org-slug]-[challenge-keyword]`
Example: `acme-logistics-corp-last-mile-visibility`

File path: `programs/[program-slug]/challenges/[challenge-slug].md`

---

## Step 7 — Create one challenge file per confirmed challenge

For each confirmed challenge, create `programs/[program-slug]/challenges/[challenge-slug].md` using the template below:

```markdown
# [Challenge Title] — Challenge Profile

## Challenge Overview

| Field | Value |
|---|---|
| Title | [Challenge title] |
| Slug | [challenge-slug] |
| Program | [program-slug] |
| Organization | [org-slug] |
| Innovation horizon | [Efficient / Sustaining / Transformative] |
| Status | open |
| Created | [Today's date — YYYY-MM-DD] |
| Owner | _To be confirmed_ |
| Executive sponsor | _To be confirmed_ |

---

## Problem Statement

[2–4 sentences describing the specific problem this challenge addresses. Include:
- Current state: what is happening today
- Gap: what is not working or missing
- Consequence: what happens if this is not solved]

_Drafted from workshop inputs. Validate and refine with the organization._

---

## Success Criteria

What does a successful solution look like? Define measurable outcomes.

| Criterion | Target | Measurement method |
|---|---|---|
| [Outcome] | [Specific, measurable target] | _To be defined_ |
| [Outcome] | [Specific, measurable target] | _To be defined_ |

_To be validated with the executive sponsor before scouting begins._

---

## Prioritization

| Axis | Score (1–5) | Rationale |
|---|---|---|
| Feasibility | [F] | [Brief reason] |
| Impact | [I] | [Brief reason] |
| **Priority score** | **[F × I]** | |

---

## Constraints

Known constraints that any solution must respect.

| Constraint | Type | Notes |
|---|---|---|
| _To be completed_ | [technical / regulatory / budget / operational] | |

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
| [Today's date] | Challenge formalized | Workshop with [org-slug] |
```

---

## Step 8 — Update program-context.md

Open `programs/[program-slug]/program-context.md` and add a **Challenges** section (or update it if it exists):

```markdown
## Challenges

| Challenge | Organization | Horizon | Score | Status |
|---|---|---|---|---|
| [Challenge title] | [org-slug] | [H1/H2/H3] | [F×I] | open |
```

Add one row per formalized challenge. If the section already exists, append the new rows.

---

## Step 9 — Confirm and suggest next step

```
Challenge workshop completed — [Organization Name]

  Program:       [program-slug]
  Organization:  [org-slug]
  Challenges formalized: [N]

Files created:
  programs/[program-slug]/challenges/[challenge-slug-1].md
  programs/[program-slug]/challenges/[challenge-slug-2].md
  ...

program-context.md updated:
  [N] challenge(s) added to the Challenges table.

Next step: run /vpm-scout to begin sourcing solvers for these challenges.
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-org-setup` | The organization profile and challenge signals must exist before the workshop runs. |
| Followed by | `/vpm-scout` | Formalized challenges are the input for solver scouting. |

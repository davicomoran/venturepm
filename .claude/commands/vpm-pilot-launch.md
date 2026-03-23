# /vpm-pilot-launch

Co-designs a pilot between a solver and an organization within a program. Generates a complete pilot design document covering problem statement, proposed solution, success criteria, KPIs, timeline, responsibilities, budget, and escalation conditions. Creates blocker entries if critical risks are detected.

---

## Step 1 — Collect inputs

### Context inference

If this command is run immediately after `/vpm-fit-check` or another `/vpm-` command in the same session, infer the program, batch, org, unit, solver, and challenge slugs from the established session context. Do not re-ask for information already known.

Display the inferred context for confirmation:

```
Context inferred from session:
  Program:    [program-slug]
  Batch:      [batch-slug or —]
  Org:        [org-slug]
  Unit:       [unit-slug or —]
  Solver:     [solver-slug]
  Challenge:  [challenge-slug]

Is this correct? (yes / no)
```

If the operator confirms, proceed. If no session context is available, ask for all inputs:

```
To design a pilot, please provide:

1. Program slug
   Example: global-innovation-sprint

2. Batch slug (optional)
   Leave blank if the organization is directly under the program.
   Example: cohort-2026-q1

3. Organization slug
   Example: acme-logistics-corp

4. Unit slug (optional)
   Leave blank if challenges belong to the organization directly.
   Example: last-mile-operations

5. Solver slug
   Example: bringg-technologies

6. Challenge slug
   Example: acme-logistics-corp-last-mile-visibility

The fit assessment will be read automatically from:
  [owner-folder]/solvers/[solver-slug]-[challenge-slug]-fit.md
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

Check that all required files exist:
- `programs/[program-slug]/program-context.md`
- `[owner-folder]/solvers/[solver-slug].md`
- `[owner-folder]/challenges/[challenge-slug].md`
- `[owner-folder]/solvers/[solver-slug]-[challenge-slug]-fit.md`

If any file is missing, stop and display:

```
Missing required file: [path]

Ensure the following commands have been run first:
  /vpm-setup-program       — to initialize the program
  /vpm-challenge-workshop  — to create challenges
  /vpm-scout               — to create solver profiles
  /vpm-fit-check           — to generate a fit assessment
```

Do not proceed if any file is missing.

---

## Step 3 — Read and validate fit assessment

Read `[owner-folder]/solvers/[solver-slug]-[challenge-slug]-fit.md` and extract:
- Overall fit score
- Dimension scores (strategic, technical, operational)
- Final recommendation (Advance to pilot / Conditional / Reject)
- Critical risks (type, likelihood, mitigation)
- Open questions and conditions (if Conditional)

**If recommendation is Reject:**

```
This solver was assessed as Reject for [challenge-slug].

Overall score: [X.X]/5

Proceeding with a pilot is not recommended.
Run /vpm-scout to identify alternative solvers for this challenge.
```

Stop. Do not proceed.

**If recommendation is Conditional:**

Display the unmet conditions and ask the operator to confirm they have been resolved:

```
This solver was assessed as Conditional for [challenge-slug].

The following conditions must be met before launching a pilot:

  [ ] [Condition 1]
  [ ] [Condition 2]

Have all conditions been resolved? (yes / no)
If yes, briefly describe how each was addressed.
```

Wait for the operator's response. If the answer is no, stop and prompt to resolve conditions first. If yes, record the resolution notes and continue.

---

## Step 4 — Read solver and challenge context

Read `[owner-folder]/solvers/[solver-slug].md` and extract:
- Value proposition
- Technology and product details
- Business model and pricing signals
- Integration feasibility (data requirements, IT/security, change management, estimated duration)
- Reference clients

Read `[owner-folder]/challenges/[challenge-slug].md` and extract:
- Challenge title and problem statement
- Organization and unit
- Success criteria
- Known constraints (budget, timeline, technical, organizational)
- Challenge owner

---

## Step 5 — Collect pilot-specific details

Present a summary of what is already known, then ask the operator for the remaining details:

```
Pilot Design — [Solver Name] × [Challenge Title]

From the fit assessment and challenge file, I have:
  Problem statement: [1-sentence summary]
  Proposed solution: [1-sentence summary]
  Fit score:         [X.X]/5 — [Recommendation]
  Est. pilot duration: [from integration feasibility or "To be defined"]

Please complete the following to finalize the pilot design:

1. Pilot owner
   Who is accountable for this pilot on the organization's side?
   (Name and role)

2. Solver contact
   Who is the main point of contact at the solver for this pilot?
   (Name and role)

3. Pilot start date
   When is the pilot expected to begin? (YYYY-MM-DD or approximate month)

4. Pilot duration
   How long will the pilot run? (e.g., 6 weeks, 3 months)

5. Budget
   What is the confirmed or estimated budget for this pilot?
   (Amount and currency, or "TBD")

6. Pilot scope
   What is explicitly in scope for this pilot?
   What is explicitly out of scope?

7. Success criteria
   What specific, measurable outcomes define a successful pilot?
   (Add to or refine what is already in the challenge file)

8. KPIs
   List 2–4 metrics that will be tracked during the pilot.
   For each: metric name, baseline (current state), and target.

9. Escalation conditions
   Under what circumstances should the pilot be paused or stopped early?
   (e.g., KPI drops below X, budget overrun, blocker unresolved after N days)
```

Wait for the operator's response before proceeding.

---

## Step 6 — Derive pilot slug and file path

Derive the pilot slug: `[solver-slug]-[challenge-slug]-pilot`

File path: `[owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md`

If a pilot file already exists at that path, notify the operator:

```
A pilot file already exists: [path]

Do you want to (A) overwrite it or (B) cancel?
```

Wait for the operator's response before proceeding.

---

## Step 7 — Generate pilot design document

Create `[owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md` using the template below.

Pre-fill every field from the context collected in previous steps. Mark any field that could not be confirmed as `_To be confirmed_`.

---

## Step 8 — Update decision log

Open `[owner-folder]/decisions/[challenge-slug]-decisions.md`. If the file does not exist, create it with this header:

```markdown
# Decision Log — [Challenge Title]

| Date | Solver | Type | Outcome | Score | Summary | Assessor |
|---|---|---|---|---|---|---|
```

Append one row for this pilot launch:

```markdown
| [YYYY-MM-DD] | [solver-slug] | pilot-launch | launch | [X.X]/5 | [1-sentence summary: what pilot was launched and primary reason solver was selected] | [Operator name or "Claude / operator"] |
```

---

## Step 9 — Log critical risks as blockers

For each risk in the fit assessment rated **High** likelihood, append a row to `[owner-folder]/blockers/[challenge-slug]-blockers.md`.

If the file does not exist, create it with this header:

```markdown
# Blocker Log — [Challenge Title]

| ID | Opened | Initiative | Type | Severity | Status | Owner | Description | Mitigation |
|---|---|---|---|---|---|---|---|---|
```

Assign each blocker an auto-incrementing ID (`B-001`, `B-002`, ...) based on the number of existing rows. Append one row per High-likelihood risk:

```markdown
| B-00N | [YYYY-MM-DD] | [solver-slug]-[challenge-slug]-pilot | [type] | high | open | _To be assigned_ | [Risk description from fit assessment] | [Mitigation from fit assessment or _To be defined_] |
```

After logging blockers, list the IDs in the confirmation summary.

---

## Step 10 — Update challenge status

Open `[owner-folder]/challenges/[challenge-slug].md` and update the `Status` field in the `## Challenge Overview` table from `in-evaluation` to `in-pilot`:

```markdown
| Status | in-pilot |
```

Also update the **Linked Pilots** section to add a row for this pilot:

```markdown
## Linked Pilots

| Pilot | Status | Start date |
|---|---|---|
| [solver-slug]-[challenge-slug]-pilot | in-design | [YYYY-MM-DD or TBD] |
```

---

## Step 11 — Confirm and suggest next step

```
Pilot launched — [Solver Name] × [Challenge Title]

  Program:     [program-slug]
  Owner:       [org-slug or unit-slug]
  Solver:      [solver-slug]
  Challenge:   [challenge-slug]
  Pilot owner: [Name, Role]
  Start date:  [YYYY-MM-DD or TBD]
  Duration:    [Duration]
  Budget:      [Amount or TBD]
  Status:      in-design

Files created:
  [owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md
  [owner-folder]/decisions/[challenge-slug]-decisions.md — 1 row appended

Blockers logged: [N] (if any)
  [owner-folder]/blockers/[challenge-slug]-blockers.md — [B-001: type, B-002: type, ...]

Challenge updated:
  [owner-folder]/challenges/[challenge-slug].md — status → in-pilot; pilot linked

Next step: run /vpm-report to generate a status report for this program or pilot.
```

---

## Output template

```markdown
# Pilot Design — [Solver Name] × [Challenge Title]

| Field | Value |
|---|---|
| Program | [program-slug] |
| Owner | [org-slug or unit-slug] |
| Solver | [solver-slug] |
| Challenge | [challenge-slug] |
| Status | in-design |
| Created | [YYYY-MM-DD] |
| Fit score | [X.X]/5 — [Advance to pilot / Conditional] |

---

## Problem Statement

[The problem this pilot is designed to solve. Drawn from the challenge file. 2–4 sentences.]

---

## Proposed Solution

[What the solver will deliver during this pilot. Focused on scope — not a full product description. 2–4 sentences.]

---

## Pilot Scope

### In scope

- [Explicit deliverable or capability included in this pilot]
- [Explicit deliverable or capability included in this pilot]

### Out of scope

- [What is explicitly excluded to keep the pilot focused]
- [What is explicitly excluded to keep the pilot focused]

---

## Team

| Role | Name | Organization |
|---|---|---|
| Pilot owner | [Name] | [Organization name] |
| Solver contact | [Name] | [Solver name] |
| Challenge owner | [Name if known] | [Organization name] |
| Program manager | [Name if known] | [Program team] |

---

## Timeline

| Milestone | Target date | Owner |
|---|---|---|
| Pilot kickoff | [YYYY-MM-DD] | [Owner] |
| [Mid-point checkpoint] | [YYYY-MM-DD] | [Owner] |
| [Key deliverable] | [YYYY-MM-DD] | [Owner] |
| Pilot review and decision | [YYYY-MM-DD] | [Pilot owner] |

Pilot duration: [Duration]

---

## Budget

| Item | Estimated cost | Status |
|---|---|---|
| Solver fees / license | [Amount or TBD] | [Confirmed / TBD] |
| Internal resources | [Amount or TBD] | [Confirmed / TBD] |
| Infrastructure / tooling | [Amount or TBD] | [Confirmed / TBD] |
| **Total** | **[Amount or TBD]** | |

---

## Success Criteria

The pilot is considered successful if the following outcomes are achieved by the end of the pilot period:

- [Specific, measurable outcome]
- [Specific, measurable outcome]
- [Specific, measurable outcome]

---

## KPIs

| Metric | Baseline | Target | Measurement method |
|---|---|---|---|
| [Metric name] | [Current state] | [Target value] | [How it will be measured] |
| [Metric name] | [Current state] | [Target value] | [How it will be measured] |

---

## Pilot Decision Framework

At the end of the pilot, a decision will be made based on KPI outcomes and qualitative assessment:

| Outcome | Condition | Next step |
|---|---|---|
| Scale | All success criteria met; KPIs at or above target | Proceed to full deployment scoping |
| Extend | Partial success; specific gaps identified with a clear path to resolution | Define extended pilot scope and timeline |
| Stop | Success criteria not met; no clear path to resolution | Close pilot; document learnings; return to scouting if needed |

---

## Escalation Conditions

The pilot will be paused or stopped early if any of the following occur:

- [Condition — e.g., primary KPI drops below X for two consecutive weeks]
- [Condition — e.g., budget overrun exceeds 20% without executive approval]
- [Condition — e.g., a High-likelihood blocker remains unresolved after 10 business days]

---

## Open Blockers

| ID | Type | Severity | Status |
|---|---|---|---|
| [B-00N] | [type] | high | open |

_See `[owner-folder]/blockers/[challenge-slug]-blockers.md` for full blocker details._

---

## Open Questions

- [Question that must be resolved during the pilot]
- [Question about integration, adoption, or measurement]

---

## Interaction Log

| Date | Type | Participants | Summary |
|---|---|---|---|
| [YYYY-MM-DD] | Pilot design created | — | Launched via /vpm-pilot-launch |
```

---

## Quality criteria

- [ ] All required inputs were validated before any file was created.
- [ ] Owner folder was derived correctly based on program / batch / org / unit inputs.
- [ ] Fit assessment was read; Reject recommendations blocked progression.
- [ ] Conditional recommendations required operator confirmation of resolved conditions.
- [ ] Pilot design includes measurable success criteria and KPIs with baselines and targets.
- [ ] All High-likelihood risks from the fit assessment were logged as blockers.
- [ ] Pilot file created at `[owner-folder]/pilots/`; blockers appended to `[owner-folder]/blockers/[challenge-slug]-blockers.md`.
- [ ] Decision log entry created at `[owner-folder]/decisions/`.
- [ ] File paths follow slug conventions: lowercase, hyphen-separated.
- [ ] Confirmation summary was shown to the operator.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-fit-check` | A scored fit assessment with an Advance or Conditional recommendation must exist before a pilot can be designed. |
| Triggered during | `/vpm-blocker-check` | High-likelihood risks from the fit assessment are converted to blockers during pilot launch. |
| Followed by | `/vpm-pilot-review` | Once a pilot is in-design, run `/vpm-pilot-review` to activate, check progress, and close the pilot with a Scale/Extend/Stop decision. |
| Followed by | `/vpm-report` | Once a pilot is in-design or active, a status report can be generated for stakeholders. |

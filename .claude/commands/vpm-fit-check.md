# /vpm-fit-check

Evaluates the fit between one or more solvers and a specific challenge across three dimensions: strategic, technical, and operational-cultural. Supports single-solver and batch modes. Generates a structured fit assessment per solver and, in batch mode, a comparative ranking at the end.

---

## Step 1 — Collect inputs

### Context inference

If this command is run immediately after `/vpm-scout` or another `/vpm-` command in the same session, infer the program, batch, org, unit, and challenge slugs from the established session context. Do not re-ask for information already known.

Display the inferred context for confirmation:

```
Context inferred from session:
  Program:    [program-slug]
  Batch:      [batch-slug or —]
  Org:        [org-slug]
  Unit:       [unit-slug or —]
  Challenge:  [challenge-slug]

Is this correct? (yes / no)
```

If the operator confirms, proceed. If no session context is available, ask for all inputs:

```
To run a fit check, please provide:

1. Program slug
   Example: innovation-program-2026

2. Batch slug (optional)
   Leave blank if the organization is directly under the program.
   Example: cohort-q1

3. Organization slug
   Example: corp-name

4. Unit slug (optional)
   Leave blank if challenges belong to the organization directly.
   Example: division-name

5. Challenge slug
   Example: org-name-challenge-slug
```

### Solver selection

Ask the operator:

```
Which solver(s) would you like to evaluate?

  — Enter one slug for a single fit check
  — Enter multiple slugs (comma-separated) for batch mode
  — Type "all" to evaluate all solvers linked to this challenge
```

Wait for the operator's response. Store the solver list for sequential processing in Steps 3–9.

---

## Step 2 — Resolve owner folder and validate

Derive the owner folder path from the inputs:

| Inputs | Owner folder |
|---|---|
| program only | `programs/[program-slug]/orgs/[org-slug]/` |
| program + batch | `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/` |
| program + org + unit | `programs/[program-slug]/orgs/[org-slug]/units/[unit-slug]/` |
| program + batch + org + unit | `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/units/[unit-slug]/` |

Check that all three files exist:
- `programs/[program-slug]/program-context.md`
- `[owner-folder]/solvers/[solver-slug].md`
- `[owner-folder]/challenges/[challenge-slug].md`

If any file is missing, stop and display:

```
Missing required file: [path]

Ensure the following commands have been run first:
  /vpm-setup-program       — to initialize the program
  /vpm-challenge-workshop  — to create challenges
  /vpm-scout               — to create solver profiles
```

Do not proceed if any file is missing.

---

## Step 3 — Read solver and challenge context

Read `[owner-folder]/solvers/[solver-slug].md` and extract:
- Company overview (size, funding stage, maturity)
- Value proposition
- Technology and product details (core tech, deployment model, integration approach, maturity level)
- Business model (revenue model, minimum engagement)
- Reference clients
- Existing fit assessment (if any — from scouting)
- Integration feasibility
- Risks and open questions
- `Data confidence` field

Read `[owner-folder]/challenges/[challenge-slug].md` and extract:
- Challenge title and problem statement
- Innovation horizon (Efficient / Sustaining / Transformative)
- Success criteria
- Known constraints (budget, timeline, technical, organizational)
- Owner slug (org or unit)

Use both documents as the primary basis for scoring. Do not ask the operator to re-enter information already present in these files.

**Data confidence check:** If the solver profile has `Data confidence: Low`, alert the operator before proceeding:

```
⚠ Data confidence: Low — [Solver Name]

This profile was generated with unconfirmed or estimated data. The fit assessment
will be based on limited information and scores should be treated as indicative only.

Proceed anyway? (yes / no)
```

If the operator proceeds, note the low confidence in the fit assessment file and apply conservative scoring where data gaps exist.

---

## Step 4 — Collect additional context

This step has two parts: importing interaction context and filling in missing fields.

### Part A — Interaction context

Always ask, regardless of whether the solver profile is complete:

```
Fit Check — [Solver Name] × [Challenge Title]

Has there been any direct interaction with this solver?
(call, presentation, demo, video call, email exchange, meeting notes)

If yes, paste or summarize the relevant content here — transcripts, deck highlights,
pricing discussed, commitments made, concerns raised.

If no, type "none" to proceed with public profile only.
```

Wait for the operator's response. If interaction context is provided, extract and incorporate it into the scoring in Step 5. Note the source in the fit assessment file (e.g., "Score informed by call transcript dated [date]").

### Part B — Missing fields

If any field critical to scoring is still marked `_To be validated_` or missing after accounting for the interaction context provided:

```
A few fields remain unconfirmed:

1. [Field] — [Why this affects the score]
2. [Field] — [Why this affects the score]

Provide what you know. Leave blank if unknown — it will be noted as a risk.
```

Wait for the operator's response. If all critical fields are now present, proceed to scoring.

---

## Step 5 — Score each dimension

**Note on scouting scores:** The solver profile may contain a preliminary fit assessment generated during `/vpm-scout`. Those scores are indicative — based on public information only, without direct contact or challenge-specific analysis. The scores produced in this step replace them as the authoritative evaluation. The fit assessment file created in Step 8 is the definitive record.

Using all available context — solver profile, challenge file, and any interaction context provided in Step 4 — score each dimension from 1 to 5 and write a 2–3 sentence rationale.

### Scoring guide

| Score | Label | Meaning |
|---|---|---|
| 5 | Excellent | Near-perfect alignment; no significant gaps |
| 4 | Strong | Clear alignment with minor gaps manageable in a pilot |
| 3 | Moderate | Meaningful alignment but material gaps that require mitigation |
| 2 | Weak | Fundamental gaps that make a successful pilot unlikely without major changes |
| 1 | Poor | No meaningful alignment; proceed would require significant deviation from challenge scope |

### Dimension definitions

**Strategic Fit** — How well the solver's direction, vision, and positioning align with the challenge and the program's goals. Consider: Is this a core use case for the solver or a stretch? Are they investing in this problem area or pivoting away? Does the innovation horizon match their product maturity?

**Technical Fit** — Whether the solver's technology can realistically address the challenge within the pilot's constraints. Consider: Does their tech stack meet the integration requirements? Is the deployment model compatible with the target organization's infrastructure? Is their product maturity appropriate for a pilot?

**Operational & Cultural Fit** — Whether the solver can work effectively with the target organization during a structured pilot. Consider: Company size mismatch, geographic and language coverage, support model and responsiveness, sales motion (PLG vs. enterprise), and willingness to commit to defined success criteria and timelines.

---

## Step 6 — Identify critical risks

List up to five critical risks that could prevent a successful pilot. For each risk:
- Classify it using the blocker taxonomy: `political`, `budget`, `technical`, `cultural`, `regulatory`, `operational`
- Rate likelihood: `High`, `Medium`, `Low`
- Propose a mitigation if one exists

---

## Step 7 — Determine final recommendation

Calculate the overall fit score:

```
Overall = (Strategic + Technical + Operational) / 3
```

Apply this decision logic:

| Condition | Recommendation |
|---|---|
| Overall ≥ 3.5 AND no High-likelihood critical risk | **Advance to pilot** |
| Overall 2.5–3.4 OR any dimension ≤ 2 OR one High-likelihood critical risk | **Conditional** |
| Overall < 2.5 OR any dimension = 1 OR two or more High-likelihood critical risks | **Reject** |

For **Conditional** recommendations, specify the conditions that must be met before advancing (e.g., technical validation, pricing confirmation, stakeholder sign-off).

---

## Step 8 — Generate fit assessment file

Create `[owner-folder]/solvers/[solver-slug]-[challenge-slug]-fit.md` using the template below.

---

## Step 9 — Update decision log

Open `[owner-folder]/decisions/[challenge-slug]-decisions.md`. If the file does not exist, create it with this header:

```markdown
# Decision Log — [Challenge Title]

| Date | Solver | Type | Outcome | Score | Summary | Assessor |
|---|---|---|---|---|---|---|
```

Append one row per solver assessed:

```markdown
| [YYYY-MM-DD] | [solver-slug] | fit-assessment | [advance / conditional / reject] | [X.X]/5 | [1-sentence rationale referencing the decisive dimension or risk] | [Operator name or "Claude / operator"] |
```

In batch mode, append one row per solver in ranking order.

---

## Step 10 — Update solver file and challenge file

**Solver file** — open `[owner-folder]/solvers/[solver-slug].md` and update the frontmatter `status` field based on the recommendation:

| Recommendation | Frontmatter update |
|---|---|
| Advance to pilot | No change — keep `status: in-evaluation` |
| Conditional | No change — keep `status: in-evaluation` |
| Reject | Update to `status: rejected` |

Only update the frontmatter block. Do not modify the file body.

**Challenge file** — open `[owner-folder]/challenges/[challenge-slug].md` and update the **Linked Solvers** section.

Find the row for `[solver-slug]` and update its status to `fit-assessed`. If no row exists, add one:

```markdown
| [solver-slug] | [Overall fit score] | fit-assessed |
```

---

## Step 11 — Confirm and suggest next step

### Single solver

```
Fit check completed — [Solver Name] × [Challenge Title]

  Program:     [program-slug]
  Owner:       [org-slug or unit-slug]
  Solver:      [solver-slug]
  Challenge:   [challenge-slug]

  Strategic fit:    [score]/5
  Technical fit:    [score]/5
  Operational fit:  [score]/5
  Overall:          [score]/5

  Recommendation:   [Advance to pilot / Conditional / Reject]

Files created or updated:
  [owner-folder]/solvers/[solver-slug]-[challenge-slug]-fit.md
  [owner-folder]/decisions/[challenge-slug]-decisions.md — 1 row appended
  [owner-folder]/challenges/[challenge-slug].md — status → fit-assessed

Next step: run /vpm-pilot-launch to design and initiate a pilot with this solver.
```

If the recommendation is **Reject**, suggest returning to `/vpm-scout` to identify alternative solvers.

If the recommendation is **Conditional**, list the open conditions and suggest resolving them before running `/vpm-pilot-launch`.

---

### Batch mode

After all solvers in the batch have been assessed, display a comparative ranking:

```
Fit check completed — [N] solvers × [Challenge Title]

  Challenge:  [challenge-slug]
  Owner:      [org-slug or unit-slug]

  Ranking:
  ─────────────────────────────────────────────────────────
  #  Solver              Strategic  Technical  Operational  Overall  Recommendation
  1  [solver-slug]       [x]/5      [x]/5      [x]/5        [x.x]/5  Advance to pilot
  2  [solver-slug]       [x]/5      [x]/5      [x]/5        [x.x]/5  Conditional
  3  [solver-slug]       [x]/5      [x]/5      [x]/5        [x.x]/5  Reject
  ...

  Files created:
    [owner-folder]/solvers/[solver-slug-1]-[challenge-slug]-fit.md
    [owner-folder]/solvers/[solver-slug-2]-[challenge-slug]-fit.md
    ...
    [owner-folder]/decisions/[challenge-slug]-decisions.md — [N] rows appended
  Challenge file updated:
    [owner-folder]/challenges/[challenge-slug].md — all assessed solvers → fit-assessed

Next step: run /vpm-pilot-launch with the top-ranked solver(s) recommended to Advance or Conditional.
```

---

## Output template

```markdown
# Fit Assessment — [Solver Name] × [Challenge Title]

| Field | Value |
|---|---|
| Program | [program-slug] |
| Owner | [org-slug or unit-slug] |
| Solver | [solver-slug] |
| Challenge | [challenge-slug] |
| Assessed | [YYYY-MM-DD] |
| Assessor | [Operator name or "Claude / operator"] |

---

## Scores

| Dimension | Score | Label |
|---|---|---|
| Strategic fit | [1–5] | [Poor / Weak / Moderate / Strong / Excellent] |
| Technical fit | [1–5] | [Poor / Weak / Moderate / Strong / Excellent] |
| Operational & cultural fit | [1–5] | [Poor / Weak / Moderate / Strong / Excellent] |
| **Overall** | **[X.X]** | |

---

## Strategic Fit

Score: [1–5] — [Label]

[2–3 sentences assessing alignment between the solver's direction and the challenge. Reference specific evidence from the solver profile and challenge definition.]

---

## Technical Fit

Score: [1–5] — [Label]

[2–3 sentences assessing technical compatibility. Reference deployment model, integration approach, maturity level, and known constraints from the challenge.]

---

## Operational & Cultural Fit

Score: [1–5] — [Label]

[2–3 sentences assessing whether this solver can operate effectively within the target organization during a pilot. Reference company size, support model, geographic coverage, and sales motion.]

---

## Critical Risks

| Risk | Type | Likelihood | Mitigation |
|---|---|---|---|
| [Describe risk] | [technical / operational / budget / cultural / regulatory / political] | [High / Medium / Low] | [Mitigation or _To be defined_] |
| [Describe risk] | [Type] | [Likelihood] | [Mitigation or _To be defined_] |

---

## Recommendation

**[Advance to pilot / Conditional / Reject]**

[2–3 sentences explaining the recommendation. Reference the overall score, the most decisive dimension, and any critical risk that influenced the decision.]

### Conditions (if Conditional)

- [ ] [Condition that must be met before advancing]
- [ ] [Condition that must be met before advancing]

### Open questions before pilot design

- [Question about scope, success criteria, or logistics]
- [Question about pricing, timeline, or resources]
- [Question about technical prerequisites or organizational readiness]
```

---

## Quality criteria

- [ ] All required inputs were validated before scoring began.
- [ ] Owner folder was derived correctly based on program / batch / org / unit inputs.
- [ ] Scores are grounded in evidence from the solver profile and challenge file — not generic assumptions.
- [ ] Each dimension rationale references specific facts, not vague language.
- [ ] Critical risks are classified using the blocker taxonomy.
- [ ] The recommendation follows the decision logic table exactly.
- [ ] Conditional recommendations include explicit, actionable conditions.
- [ ] The challenge file was updated with the new evaluation status.
- [ ] Fit file created at `[owner-folder]/solvers/`.
- [ ] Decision log entry created at `[owner-folder]/decisions/`.
- [ ] The confirmation summary was shown to the operator.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-scout` | Solver profiles must exist before a structured fit evaluation can be run. |
| Followed by | `/vpm-pilot-launch` | An Advance or Conditional recommendation is the trigger for pilot design and launch. |

# /vpm-fit-check

Evaluates the fit between a solver and a specific challenge across three dimensions: strategic, technical, and operational-cultural. Generates a structured fit assessment with dimension scores, critical risks, and a final recommendation (Advance to pilot / Conditional / Reject).

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To run a fit check, please provide:

1. Program slug
   The slug of the active program.
   Example: global-innovation-sprint

2. Solver slug
   The slug of the solver to evaluate.
   Example: bringg-technologies

3. Challenge slug
   The slug of the challenge to evaluate the solver against.
   Example: acme-logistics-corp-last-mile-visibility
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate inputs

Check that all three files exist:
- `programs/[program-slug]/program-context.md`
- `programs/[program-slug]/solvers/[solver-slug].md`
- `programs/[program-slug]/challenges/[challenge-slug].md`

If any file is missing, stop and display:

```
Missing required file: [path]

Ensure the following commands have been run first:
  /vpm-setup-program   — to initialize the program
  /vpm-challenge-workshop — to create challenges
  /vpm-scout           — to create solver profiles
```

Do not proceed if any file is missing.

---

## Step 3 — Read solver and challenge context

Read `programs/[program-slug]/solvers/[solver-slug].md` and extract:
- Company overview (size, funding stage, maturity)
- Value proposition
- Technology and product details (core tech, deployment model, integration approach, maturity level)
- Business model (revenue model, minimum engagement)
- Reference clients
- Existing fit assessment (if any — from scouting)
- Integration feasibility
- Risks and open questions

Read `programs/[program-slug]/challenges/[challenge-slug].md` and extract:
- Challenge title and problem statement
- Innovation horizon (Efficient / Sustaining / Transformative)
- Success criteria
- Known constraints (budget, timeline, technical, organizational)
- Organization slug and unit (if applicable)

Use both documents as the primary basis for scoring. Do not ask the operator to re-enter information already present in these files.

---

## Step 4 — Prompt for unknowns

If any field critical to scoring is marked `_To be validated_` or is missing, ask the operator to fill in the gaps before proceeding:

```
Fit Check — [Solver Name] × [Challenge Title]

Before scoring, please clarify the following:

1. [Field] — [Why this affects the score]
2. [Field] — [Why this affects the score]

Provide what you know. Leave blank if unknown — it will be noted as a risk.
```

Wait for the operator's response. If all critical fields are present, skip this step.

---

## Step 5 — Score each dimension

Using all available context, score each dimension from 1 to 5 and write a 2–3 sentence rationale.

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

Create `programs/[program-slug]/solvers/[solver-slug]-[challenge-slug]-fit.md` using the template below.

---

## Step 9 — Update challenge file

Open `programs/[program-slug]/challenges/[challenge-slug].md` and update the **Linked Solvers** section.

Find the row for `[solver-slug]` and update its status to `fit-assessed`. If no row exists, add one:

```markdown
| [solver-slug] | [Overall fit score] | fit-assessed |
```

---

## Step 10 — Confirm and suggest next step

```
Fit check completed — [Solver Name] × [Challenge Title]

  Program:     [program-slug]
  Solver:      [solver-slug]
  Challenge:   [challenge-slug]

  Strategic fit:    [score]/5
  Technical fit:    [score]/5
  Operational fit:  [score]/5
  Overall:          [score]/5

  Recommendation:   [Advance to pilot / Conditional / Reject]

Files created or updated:
  programs/[program-slug]/solvers/[solver-slug]-[challenge-slug]-fit.md
  programs/[program-slug]/challenges/[challenge-slug].md — evaluation status updated

Next step: run /vpm-pilot-launch to design and initiate a pilot with this solver.
```

If the recommendation is **Reject**, suggest returning to `/vpm-scout` to identify alternative solvers.

If the recommendation is **Conditional**, list the open conditions and suggest resolving them before running `/vpm-pilot-launch`.

---

## Output template

```markdown
# Fit Assessment — [Solver Name] × [Challenge Title]

| Field | Value |
|---|---|
| Program | [program-slug] |
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
- [ ] Scores are grounded in evidence from the solver profile and challenge file — not generic assumptions.
- [ ] Each dimension rationale references specific facts, not vague language.
- [ ] Critical risks are classified using the blocker taxonomy.
- [ ] The recommendation follows the decision logic table exactly.
- [ ] Conditional recommendations include explicit, actionable conditions.
- [ ] The challenge file was updated with the new evaluation status.
- [ ] The confirmation summary was shown to the operator.

---

## Usage example

```
Operator: /vpm-fit-check

Claude: To run a fit check, please provide:
        1. Program slug
        2. Solver slug
        3. Challenge slug

Operator: program: global-innovation-sprint
          solver: bringg-technologies
          challenge: acme-logistics-corp-last-mile-visibility

Claude: [Reads both files, scores each dimension, identifies risks, applies decision logic, creates fit file, updates challenge file, shows confirmation summary]
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-scout` | Solver profiles must exist before a structured fit evaluation can be run. |
| Followed by | `/vpm-pilot-launch` | An Advance or Conditional recommendation is the trigger for pilot design and launch. |

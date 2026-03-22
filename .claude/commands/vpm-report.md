# /vpm-report

Generates a periodic executive report for a program. Reads all active files across challenges, solvers, pilots, blockers, and decisions to produce a structured summary covering headline metrics, pipeline status, key advances, open blockers, decisions made, learnings, and prioritized next steps.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To generate a program report, please provide:

1. Program slug
   The slug of the program to report on.
   Example: global-innovation-sprint

2. Period
   The reporting period.
   Format: YYYY-MM for monthly, YYYY-QN for quarterly.
   Examples: 2026-03 / 2026-Q1

3. Report audience (optional)
   Who will read this report?
   Examples: executive committee, program team, board, external partners
   Default: executive committee
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate program

Check that `programs/[program-slug]/program-context.md` exists.

If not found, stop and display:

```
Program "[program-slug]" not found.
Run /vpm-setup-program to initialize it first.
```

Read `program-context.md` and extract:
- Program name, mode, and start date
- Team composition
- Stated success metrics and targets
- Any program-level notes or constraints

---

## Step 3 — Scan and read all program files

Traverse the full nested hierarchy under `programs/[program-slug]/` to find all owner-level entities. Owner-level entities are the folders that contain operational subdirectories (`challenges/`, `solvers/`, `pilots/`, `blockers/`, `decisions/`). The hierarchy may include:

```
programs/[program]/orgs/[org]/
programs/[program]/orgs/[org]/units/[unit]/
programs/[program]/batches/[batch]/orgs/[org]/
programs/[program]/batches/[batch]/orgs/[org]/units/[unit]/
```

For each owner-level entity found, read every file in its operational subdirectories. If a directory does not exist or is empty, note it as having no entries.

**challenges/** — For each challenge file, extract:
- Title, owner (org or unit), innovation horizon, priority score
- Status (active / completed / blocked)
- Linked solvers and their statuses
- Open blockers referenced

**solvers/** — For each solver profile file (not fit assessment files), extract:
- Name, status, linked challenges
- Overall fit score per challenge
- Recommendation (Advance / Conditional / Reject) if assessed

**solvers/** — For each fit assessment file (`*-fit.md`), extract:
- Solver × challenge pair
- Dimension scores and overall score
- Final recommendation
- Date assessed

**pilots/** — For each pilot file, extract:
- Title, solver, challenge, pilot owner
- Status (in-design / active / completed)
- Start date and duration
- Success criteria and KPIs with current status if available
- Decision outcome if completed (scale / extend / stop)
- Open blockers referenced

**blockers/** — For each blocker file, extract:
- Title, type, severity, status, owner
- Initiative it is linked to
- Date opened and target resolution date
- Whether it falls within the reporting period

**decisions/** — For each decision file dated within the reporting period, extract:
- Decision made, date, owner
- Outcome (advance / reject / scale / extend / stop)
- Rationale summary

---

## Step 4 — Compute headline metrics

From the data gathered, compute:

| Metric | Value |
|---|---|
| Active challenges | Count of challenges with status `active` |
| Completed challenges | Count with status `completed` |
| Blocked challenges | Count with an open blocker |
| Solvers evaluated | Count of solver profiles in the program |
| Solvers advanced | Count with recommendation `Advance to pilot` |
| Solvers rejected | Count with recommendation `Reject` |
| Pilots in design | Count with status `in-design` |
| Pilots active | Count with status `active` |
| Pilots completed | Count with status `completed` |
| Pilots scaled | Count of completed pilots with outcome `scale` |
| Open blockers | Count with status `open` or `in-progress` |
| High-severity blockers | Count with severity `high` |
| Decisions this period | Count of decisions within the reporting period |

---

## Step 5 — Identify top advances

From the data, identify the 3 most significant positive developments during the reporting period. Prioritize:
1. Pilots that scaled or were extended
2. Solvers that advanced to pilot
3. Challenges that were completed or had high-quality solvers identified
4. Blockers that were resolved at high severity

For each, write 2–3 sentences explaining what happened and why it matters.

---

## Step 6 — Identify top open blockers

From the blocker files, select up to 3 open blockers ranked by severity (`high` first, then `medium`). For each, summarize:
- What is blocked
- Why it matters
- Current resolution status
- Owner and target resolution date

---

## Step 7 — Synthesize learnings

Based on the full picture of the period — what progressed, what stalled, what decisions were made, and what blockers emerged — identify 3–5 learnings. Frame each as an actionable insight, not just an observation.

Use this structure for each learning:
- **What happened**: The pattern or event observed
- **Why it matters**: The implication for the program
- **What to change**: A concrete adjustment for the next cycle

---

## Step 8 — Generate prioritized next steps

Produce a prioritized list of 3–5 recommended actions for the next reporting period. Each next step must include:
- A specific action (not generic guidance)
- A responsible party (owner role or name)
- A suggested timeframe

Prioritize: unblocking high-severity blockers, advancing pilots close to a decision, re-scouting challenges where all solvers were rejected.

---

## Step 9 — Derive report slug and file path

Derive the report slug from the period:
- Monthly: `[YYYY-MM]-report`
- Quarterly: `[YYYY-QN]-report`

File path: `programs/[program-slug]/decisions/[report-slug].md`

If a report file already exists at that path, notify the operator:

```
A report for this period already exists: [path]

Do you want to (A) overwrite it or (B) cancel?
```

Wait for the operator's response before proceeding.

---

## Step 10 — Generate report file

Create `programs/[program-slug]/decisions/[report-slug].md` using the template below.

Populate every section from the data and synthesis in previous steps. Do not leave placeholder text — if a section has no data, state that explicitly (e.g., "No pilots completed this period.").

---

## Step 11 — Confirm and suggest next step

```
Report generated — [Program name] / [Period]

  Program:   [program-slug]
  Period:    [period]
  Audience:  [audience]

  Challenges:     [active] active / [completed] completed / [blocked] blocked
  Solvers:        [evaluated] evaluated / [advanced] advanced / [rejected] rejected
  Pilots:         [active] active / [completed] completed / [scaled] scaled
  Open blockers:  [N] ([high] high severity)
  Decisions:      [N] this period

File created:
  programs/[program-slug]/decisions/[report-slug].md

Next step: share this report with stakeholders, then run /vpm-challenge-workshop
to open or reprioritize challenges for the next cycle.
```

---

## Output template

```markdown
# [Program Name] — [Period] Report

| Field | Value |
|---|---|
| Program | [program-slug] |
| Mode | [venture-client / venture-building / hybrid] |
| Period | [period] |
| Audience | [audience] |
| Generated | [YYYY-MM-DD] |

---

## Headline Metrics

| Metric | This period | Total to date |
|---|---|---|
| Active challenges | [N] | [N] |
| Solvers evaluated | [N] | [N] |
| Solvers advanced to pilot | [N] | [N] |
| Pilots active | [N] | [N] |
| Pilots scaled | [N] | [N] |
| Open blockers | [N] | [N] |
| High-severity blockers | [N] | [N] |
| Decisions made | [N] | [N] |

---

## Pipeline Summary

### Phase 2 — Challenges

| Challenge | Organization | Owner | Horizon | Priority | Status |
|---|---|---|---|---|---|
| [challenge-slug] | [org] | [owner] | [Efficient / Sustaining / Transformative] | [score] | [active / completed / blocked] |

### Phase 3 — Scouting

| Solver | Challenge | Source | Status |
|---|---|---|---|
| [solver-slug] | [challenge-slug] | [referral / inbound / suggestion] | [in-evaluation / advanced / rejected] |

### Phase 4 — Evaluation

| Solver | Challenge | Strategic | Technical | Operational | Overall | Recommendation |
|---|---|---|---|---|---|---|
| [solver-slug] | [challenge-slug] | [1–5] | [1–5] | [1–5] | [X.X] | [Advance / Conditional / Reject] |

### Phase 5 — Pilots

| Pilot | Solver | Owner | Status | Start date | KPI status |
|---|---|---|---|---|---|
| [pilot-slug] | [solver-slug] | [owner] | [in-design / active / completed] | [date] | [On track / At risk / Off track] |

---

## Top Advances This Period

### 1. [Title of advance]

[2–3 sentences describing what happened and why it matters for the program.]

### 2. [Title of advance]

[2–3 sentences describing what happened and why it matters for the program.]

### 3. [Title of advance]

[2–3 sentences describing what happened and why it matters for the program.]

---

## Open Blockers

### High severity

| Blocker | Type | Initiative | Owner | Opened | Target resolution |
|---|---|---|---|---|---|
| [blocker-slug] | [type] | [initiative-slug] | [owner] | [date] | [date] |

### Medium severity

| Blocker | Type | Initiative | Owner | Opened | Target resolution |
|---|---|---|---|---|---|
| [blocker-slug] | [type] | [initiative-slug] | [owner] | [date] | [date] |

_No low-severity blockers are surfaced in executive reports. See `programs/[program-slug]/blockers/` for full detail._

---

## Decisions This Period

| Date | Decision | Initiative | Outcome | Owner |
|---|---|---|---|---|
| [YYYY-MM-DD] | [Description] | [initiative-slug] | [advance / reject / scale / extend / stop] | [owner] |

---

## Learnings

### 1. [Learning title]

**What happened:** [The pattern or event observed.]
**Why it matters:** [The implication for the program.]
**What to change:** [A concrete adjustment for the next cycle.]

### 2. [Learning title]

**What happened:** [The pattern or event observed.]
**Why it matters:** [The implication for the program.]
**What to change:** [A concrete adjustment for the next cycle.]

### 3. [Learning title]

**What happened:** [The pattern or event observed.]
**Why it matters:** [The implication for the program.]
**What to change:** [A concrete adjustment for the next cycle.]

---

## Prioritized Next Steps

| # | Action | Owner | Timeframe |
|---|---|---|---|
| 1 | [Specific action] | [Owner] | [Timeframe] |
| 2 | [Specific action] | [Owner] | [Timeframe] |
| 3 | [Specific action] | [Owner] | [Timeframe] |
| 4 | [Specific action] | [Owner] | [Timeframe] |
| 5 | [Specific action] | [Owner] | [Timeframe] |
```

---

## Quality criteria

- [ ] All files in challenges/, solvers/, pilots/, blockers/, and decisions/ were read before generating the report.
- [ ] Headline metrics were computed from actual file data — not estimated.
- [ ] Top advances and top blockers were selected and ranked by impact and severity.
- [ ] Learnings are framed as actionable insights, not just observations.
- [ ] Next steps are specific: named owner, concrete action, and a timeframe.
- [ ] No placeholder text was left in the generated report — missing data is stated explicitly.
- [ ] The report file was saved to `decisions/` with the correct slug format.
- [ ] Confirmation summary was shown to the operator.

---

## Usage example

```
Operator: /vpm-report

Claude: To generate a program report, please provide:
        1. Program slug
        2. Period
        3. Report audience (optional)

Operator: program: global-innovation-sprint
          period: 2026-Q1
          audience: executive committee

Claude: [Reads all program files, computes metrics, identifies advances and blockers,
         synthesizes learnings, generates prioritized next steps, creates report file,
         shows confirmation summary]
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-pilot-launch`, `/vpm-blocker-check` | Reports are most useful once pilots are active and blockers are logged — they synthesize the full program state. |
| Followed by | `/vpm-challenge-workshop` | Learnings and next steps from the report feed directly into the next cycle's challenge identification and prioritization. |

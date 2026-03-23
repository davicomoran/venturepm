# /vpm-pilot-review

Manages the lifecycle of an active pilot from launch to close. Supports two modes: (1) status update — transitions a pilot from `in-design` to `active`, or records a mid-pilot progress check; (2) pilot close — captures the final decision (Scale / Extend / Stop), updates all linked files, and creates a decision log entry. This command completes the pipeline phase started by `/vpm-pilot-launch`.

---

## Step 1 — Collect inputs

### Context inference

If this command is run immediately after `/vpm-pilot-launch` or another `/vpm-` command in the same session, infer the program, batch, org, unit, solver, and challenge slugs from the established session context. Do not re-ask for information already known.

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
To review a pilot, please provide:

1. Program slug
   Example: global-innovation-sprint

2. Batch slug (optional)
   Leave blank if the organization is directly under the program.
   Example: cohort-2026-q1

3. Organization slug
   Example: acme-logistics-corp

4. Unit slug (optional)
   Leave blank if the pilot belongs to the organization directly.
   Example: last-mile-operations

5. Solver slug
   Example: bringg-technologies

6. Challenge slug
   Example: acme-logistics-corp-last-mile-visibility
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

Derive the pilot slug: `[solver-slug]-[challenge-slug]-pilot`

Check that the pilot file exists:
- `[owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md`

If not found, stop and display:

```
Missing pilot file: [path]

Run /vpm-pilot-launch first to design and launch the pilot.
```

Read the pilot file and extract:
- Current status (`in-design` / `active` / `completed`)
- Pilot owner and solver contact
- Start date and duration
- KPIs with baselines and targets
- Escalation conditions
- Open blockers

---

## Step 3 — Determine review mode

Present the current pilot status and ask the operator what kind of review this is:

```
Pilot Review — [Solver Name] × [Challenge Title]

  Current status:  [in-design / active]
  Pilot owner:     [Name, Role]
  Start date:      [date or TBD]
  Duration:        [duration]

What would you like to do?

  A) Activate pilot     — move status from in-design to active; confirm start date
  B) Progress check     — record a mid-pilot update (KPI status, blockers, notes)
  C) Close pilot        — record the final decision (Scale / Extend / Stop)

Type A, B, or C.
```

Wait for the operator's response. If the current status is `completed`, stop and display:

```
This pilot has already been closed.
  Outcome: [outcome from pilot file]
  Date:    [date]

To view the record, open: [owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md
```

---

## Step 4A — Activate pilot (Mode A)

Ask the operator to confirm the activation details:

```
Activating pilot — [Solver Name] × [Challenge Title]

Confirm or update the following:

1. Start date (YYYY-MM-DD)
   [Current value or TBD]

2. Any changes to KPI baselines or targets since launch?
   (yes — describe / no)

3. Any open blockers to record at activation?
   (yes — describe / no)
```

Wait for the operator's response.

Update the pilot file:
- Change `status` in the frontmatter block from `in-design` to `active`
- Change `Status` in the `## Pilot Design` table from `in-design` to `active`
- Update `Start date` if confirmed or changed
- Update any KPI baseline or target changes
- Add a row to the pilot's Interaction Log

Update the challenge file:
- The challenge status should already be `in-pilot` from `/vpm-pilot-launch`. No change needed.

Proceed to Step 6 (confirmation).

---

## Step 4B — Progress check (Mode B)

Ask the operator:

```
Progress Check — [Solver Name] × [Challenge Title]

1. Check-in date (YYYY-MM-DD)

2. KPI status
   For each KPI: current value and trend (on track / at risk / off track)

3. Blockers
   Any new or unresolved blockers? (yes — describe / no)

4. Key updates
   What has happened since the last check-in? (max 3–5 bullet points)

5. Overall status
   On track / At risk / Off track
```

Wait for the operator's response.

Update the pilot file:
- Add a `## Progress Log` section if it does not exist, or append a new entry:

```markdown
## Progress Log

### [YYYY-MM-DD] — [On track / At risk / Off track]

**KPI status:**
| Metric | Baseline | Target | Current | Trend |
|---|---|---|---|---|
| [Metric] | [Baseline] | [Target] | [Current] | [On track / At risk / Off track] |

**Updates:**
- [Key update]
- [Key update]

**Open blockers:** [None / brief description of any open blockers]
```

If new blockers were reported, prompt the operator to run `/vpm-blocker-check` for each.

Proceed to Step 6 (confirmation).

---

## Step 4C — Close pilot (Mode C)

Ask the operator for the final pilot decision:

```
Closing pilot — [Solver Name] × [Challenge Title]

1. Close date (YYYY-MM-DD)

2. Final decision
   Scale  — all success criteria met; KPIs at or above target; proceeding to full deployment
   Extend — partial success; specific gaps identified; running an extended pilot
   Stop   — success criteria not met; no clear path to resolution; closing this initiative

   Type Scale, Extend, or Stop.

3. Decision rationale
   What drove this decision? (2–4 sentences — reference KPI outcomes and key observations)

4. Final KPI outcomes
   For each KPI: final measured value and whether the target was met

5. Key learnings (2–4 bullet points)
   What did you learn from this pilot that applies to future initiatives?

6. Next step
   If Scale: what is the scope and owner for full deployment?
   If Extend: what are the new scope, timeline, and success criteria for the extension?
   If Stop: should the challenge return to evaluation (re-scout) or be closed?
```

Wait for the operator's full response before proceeding.

---

## Step 5 — Update pilot file (Mode C only)

Open the pilot file and make the following updates:

- Change `status` in the frontmatter block to `completed`
- Change `Status` in the table to `completed`
- Add `Close date: [YYYY-MM-DD]`
- Add `Outcome: [scale / extend / stop]`
- Add a `## Pilot Outcome` section:

```markdown
## Pilot Outcome

| Field | Value |
|---|---|
| Close date | [YYYY-MM-DD] |
| Decision | [Scale / Extend / Stop] |
| Overall KPI result | [Met / Partially met / Not met] |

### Final KPI Results

| Metric | Baseline | Target | Final | Met? |
|---|---|---|---|---|
| [Metric] | [Baseline] | [Target] | [Final] | [Yes / No] |

### Decision Rationale

[Operator's rationale — 2–4 sentences.]

### Key Learnings

- [Learning]
- [Learning]

### Next Step

[Scale: deployment scope and owner / Extend: new scope and timeline / Stop: re-scout or close challenge]
```

Also update the challenge file based on the decision. For each update, change both the frontmatter `status` field and the `Status` row in the `## Challenge Overview` table:

| Decision | Challenge status update |
|---|---|
| Scale | `status: completed`; update Linked Pilots row to `completed` |
| Extend | Keep `status: in-pilot`; update Linked Pilots row to `extending`; note new timeline |
| Stop (re-scout) | `status: in-evaluation`; update Linked Pilots row to `stopped` |
| Stop (close) | `status: completed`; update Linked Pilots row to `stopped` |

---

## Step 6 — Update decision log (Mode C only)

Open `[owner-folder]/decisions/[challenge-slug]-decisions.md`. If the file does not exist, create it with this header:

```markdown
# Decision Log — [Challenge Title]

| Date | Solver | Type | Outcome | Score | Summary | Assessor |
|---|---|---|---|---|---|---|
```

Append one row for this pilot close:

```markdown
| [YYYY-MM-DD] | [solver-slug] | pilot-close | [scale / extend / stop] | [KPI: Met / Partial / Not met] | [1-sentence rationale referencing the decisive KPI outcome] | [Pilot owner name or "Claude / operator"] |
```

---

## Step 7 — Confirm and suggest next step

### Mode A — Activation

```
Pilot activated — [Solver Name] × [Challenge Title]

  Program:     [program-slug]
  Owner:       [org-slug or unit-slug]
  Solver:      [solver-slug]
  Challenge:   [challenge-slug]
  Status:      active
  Start date:  [YYYY-MM-DD]

Files updated:
  [owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md — status → active

Next step: run /vpm-pilot-review again when a progress check is due, or at pilot close.
```

### Mode B — Progress check

```
Progress check recorded — [Solver Name] × [Challenge Title]

  Program:     [program-slug]
  Owner:       [org-slug or unit-slug]
  Check-in:    [YYYY-MM-DD]
  Overall:     [On track / At risk / Off track]

Files updated:
  [owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md — progress log updated

[If new blockers:] Run /vpm-blocker-check for each new blocker identified.
Next step: run /vpm-pilot-review again at pilot close.
```

### Mode C — Pilot close

```
Pilot closed — [Solver Name] × [Challenge Title]

  Program:     [program-slug]
  Owner:       [org-slug or unit-slug]
  Solver:      [solver-slug]
  Challenge:   [challenge-slug]
  Decision:    [Scale / Extend / Stop]
  KPI result:  [Met / Partially met / Not met]

Files updated:
  [owner-folder]/pilots/[solver-slug]-[challenge-slug]-pilot.md — status → completed
  [owner-folder]/challenges/[challenge-slug].md — status → [completed / in-evaluation / in-pilot]
  [owner-folder]/decisions/[challenge-slug]-decisions.md — 1 row appended

[If Scale:] Next step: run /vpm-report to include this pilot's outcome in the program report.
[If Extend:] Next step: run /vpm-pilot-launch to design the extended pilot.
[If Stop — re-scout:] Next step: run /vpm-scout to identify new solver candidates for this challenge.
[If Stop — close:] Next step: run /vpm-report to document the learning and close this challenge.
```

---

## Quality criteria

- [ ] Owner folder was derived correctly based on program / batch / org / unit inputs.
- [ ] Pilot file was validated before any action was taken.
- [ ] The review mode (A / B / C) was selected by the operator before any updates were made.
- [ ] For Mode A: pilot status updated to `active`; start date confirmed.
- [ ] For Mode B: progress log entry added with KPI status and updates.
- [ ] For Mode C: pilot outcome section added; pilot status updated to `completed`.
- [ ] For Mode C: challenge status updated based on the decision logic table.
- [ ] For Mode C: decision log entry created at `[owner-folder]/decisions/`.
- [ ] Confirmation summary was shown to the operator.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-pilot-launch` | A pilot must be designed and launched before it can be reviewed or closed. |
| Triggered during | `/vpm-blocker-check` | New blockers identified during a progress check should be diagnosed with `/vpm-blocker-check`. |
| Followed by (Scale) | `/vpm-report` | A scaled pilot is a headline outcome — surface it in the next program report. |
| Followed by (Extend) | `/vpm-pilot-launch` | Design the extended pilot as a new pilot document. |
| Followed by (Stop — re-scout) | `/vpm-scout` | Return to scouting if the challenge still needs a solution. |
| Followed by (any) | `/vpm-report` | Pilot outcomes, learnings, and decisions feed directly into the program report. |

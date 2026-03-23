# /vpm-blocker-check

Diagnoses why a challenge, solver evaluation, or pilot is not progressing. Classifies the blocker using the standard taxonomy, assesses severity, assigns an owner, and recommends concrete next steps with deadlines to unblock the initiative.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To diagnose a blocker, please provide:

1. Program slug
   Example: global-innovation-sprint

2. Batch slug (optional)
   Leave blank if the organization is directly under the program.
   Example: cohort-2026-q1

3. Organization slug
   Example: acme-logistics-corp

4. Unit slug (optional)
   Leave blank if the initiative belongs to the organization directly.
   Example: last-mile-operations

5. Initiative type and slug
   What is blocked, and what is its slug?
   Type: challenge / solver / pilot
   Example: pilot / bringg-technologies-last-mile-visibility-pilot

6. Current status description
   What is happening (or not happening)?
   Be as specific as possible — describe the symptom, who is involved, and how long it has been blocked.
   Example: "The IT security team has not responded to the data access request sent 3 weeks ago.
             The pilot cannot proceed without their sign-off."
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

Determine the initiative file path from the initiative type:
- `challenge` → `[owner-folder]/challenges/[initiative-slug].md`
- `solver` → `[owner-folder]/solvers/[initiative-slug].md`
- `pilot` → `[owner-folder]/pilots/[initiative-slug].md`

Check that both files exist:
- `programs/[program-slug]/program-context.md`
- `[owner-folder]/[type]/[initiative-slug].md`

If either is missing, stop and display:

```
Missing required file: [path]

Ensure the program and initiative have been initialized before running a blocker check.
Run /vpm-setup-program, /vpm-challenge-workshop, /vpm-scout, or /vpm-pilot-launch as appropriate.
```

---

## Step 3 — Read initiative context

Read the initiative file and extract all relevant context:
- For a **challenge**: title, problem statement, owner, status, linked solvers, open questions
- For a **solver**: name, status, linked challenges, fit scores, open questions, interaction log
- For a **pilot**: title, pilot owner, timeline, KPIs, escalation conditions, open blockers already listed

Use this context alongside the operator's status description to inform the diagnosis.

---

## Step 4 — Diagnose the blocker

Using the operator's description and the initiative context, classify the blocker:

### Blocker taxonomy

| Type | Description |
|---|---|
| `political` | Internal stakeholders are misaligned or withholding support. |
| `budget` | Funding is unavailable, frozen, or pending approval. |
| `technical` | Integration, infrastructure, or technical feasibility issues. |
| `cultural` | Resistance to change, risk aversion, or adoption challenges within the organization. |
| `regulatory` | Legal, compliance, data privacy, or procurement constraints. |
| `operational` | Process gaps, resource constraints, or capacity issues blocking execution. |

A blocker may have a **primary type** and a **contributing type** if multiple dimensions are involved.

### Severity assessment

Severity measures the **impact** of the blocker if left unresolved. This is distinct from likelihood (probability the risk materializes), which is used upstream in fit assessments.

| Severity | Criteria |
|---|---|
| `high` | The initiative cannot proceed at all without resolving this blocker. Risk of full stall or cancellation. |
| `medium` | The initiative is significantly delayed but partial progress is still possible. Resolution is urgent. |
| `low` | The initiative is slowed but not stopped. Resolution can be scheduled without immediate escalation. |

If the classification is unclear from the available information, ask one targeted clarifying question before proceeding:

```
To classify this blocker accurately, I need to know:

[Single specific question — e.g., "Has the budget been formally approved or is it still pending sign-off?"]
```

Wait for the operator's response before finalizing the diagnosis.

---

## Step 5 — Generate recommended next steps

Based on the blocker type and severity, generate 2–4 concrete next steps. Each step must include:
- A specific action (not generic advice)
- A responsible party (owner role or name if known)
- A suggested deadline (relative: e.g., "within 3 business days")

Use these resolution patterns as guidance:

| Type | Common resolution paths |
|---|---|
| `political` | Escalate to executive sponsor; schedule alignment meeting; surface the decision that needs to be made |
| `budget` | Identify the approval path; prepare a budget justification; propose a reduced-scope pilot to lower the threshold |
| `technical` | Request a technical discovery session; loop in IT/security early; propose a sandboxed proof-of-concept |
| `cultural` | Identify internal champions; run a stakeholder mapping; propose a low-commitment demo or reference visit |
| `regulatory` | Engage legal or compliance team; identify the specific regulation or policy in question; request a DPA review |
| `operational` | Map the process gap; identify who owns the constraint; propose a workaround or interim solution |

---

## Step 6 — Log blocker

Open `[owner-folder]/blockers/[challenge-slug]-blockers.md`.

If the file does not exist, create it with this header:

```markdown
# Blocker Log — [Challenge Title]

| ID | Opened | Initiative | Type | Severity | Status | Owner | Description | Mitigation |
|---|---|---|---|---|---|---|---|---|
```

Assign the next auto-incrementing ID (`B-001`, `B-002`, ...) based on existing rows. Append one row:

```markdown
| B-00N | [YYYY-MM-DD] | [initiative-type]/[initiative-slug] | [type] | [high / medium / low] | open | [Owner name or _To be assigned_] | [Description] | [Mitigation or _To be defined_] |
```

**Updating an existing blocker:** If the operator is reporting a status change on a previously logged blocker (e.g., resolved, owner assigned), edit the existing row in place rather than appending a new one.

---

## Step 7 — Add resolution note to initiative file

Open the initiative file and update or add an **Open Blockers** reference:

```markdown
## Open Blockers

| ID | Type | Severity | Status |
|---|---|---|---|
| [B-00N] | [type] | [high / medium / low] | open |
```

Reference the blocker log for full detail: `[owner-folder]/blockers/[challenge-slug]-blockers.md`

If the section already exists, append the new row without removing existing entries.

---

## Step 8 — Confirm and suggest next step

```
Blocker logged — [Initiative slug]

  Program:    [program-slug]
  Owner:      [org-slug or unit-slug]
  Initiative: [type] / [initiative-slug]
  ID:         [B-00N]
  Type:       [primary type] (+ [contributing type] if applicable)
  Severity:   [high / medium / low]
  Owner:      [Owner name or "To be assigned"]
  Status:     open

Recommended next steps:
  1. [Action] — [Owner] — by [deadline]
  2. [Action] — [Owner] — by [deadline]
  3. [Action] — [Owner] — by [deadline]

Files created or updated:
  [owner-folder]/blockers/[challenge-slug]-blockers.md — 1 row appended (ID: B-00N)
  [owner-folder]/[type]/[initiative-slug].md — blocker reference added

Next step: run /vpm-report to include this blocker in a stakeholder status report.
```

---

## Output format

Blockers are stored as rows in `[owner-folder]/blockers/[challenge-slug]-blockers.md`. The file is a running log — one row per blocker, status updated in place.

**File header (created on first blocker):**

```markdown
# Blocker Log — [Challenge Title]

| ID | Opened | Initiative | Type | Severity | Status | Owner | Description | Mitigation |
|---|---|---|---|---|---|---|---|---|
```

**Row format:**

```markdown
| B-001 | [YYYY-MM-DD] | [challenge/solver/pilot]/[initiative-slug] | [type] | [high/medium/low] | [open/in-progress/resolved] | [Owner or _To be assigned_] | [Description — what is blocked, root cause hypothesis, evidence] | [Mitigation or _To be defined_] |
```

**Updating a blocker:** edit the existing row directly — change `Status`, `Owner`, or `Mitigation` as the situation evolves. Do not append a duplicate row.

The full diagnosis (recommended next steps, escalation path, resolution notes) is surfaced in the confirmation summary and in `/vpm-report` — not stored redundantly in the file.

---

## Quality criteria

- [ ] Owner folder was derived correctly based on program / batch / org / unit inputs.
- [ ] The initiative file was validated before any diagnosis was made.
- [ ] The blocker was classified using the standard taxonomy.
- [ ] Severity was assessed using the defined criteria — not assigned arbitrarily.
- [ ] Recommended next steps are specific: named owner, concrete action, and a deadline.
- [ ] A row was appended to `[owner-folder]/blockers/[challenge-slug]-blockers.md` with an auto-incremented ID.
- [ ] The initiative file was updated with a blocker reference.
- [ ] Confirmation summary was shown with next steps clearly listed.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | Any phase | Blockers can arise at any point in the pipeline — during challenge definition, solver evaluation, or an active pilot. |
| Triggered by | `/vpm-pilot-launch` | High-likelihood risks from a fit assessment are automatically converted to blockers during pilot launch. |
| Followed by | `/vpm-report` | Logged blockers are surfaced in stakeholder reports to drive visibility and resolution accountability. |

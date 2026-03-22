# /vpm-setup-unit

Adds a business unit or sub-team to an existing organization. Supports three input modes: manual entry, document import, or web search. Units are the lowest level of the hierarchy and own all operational artefacts (challenges, solvers, pilots, blockers, decisions). When the first unit is added to an org that already has artefacts, those artefacts are migrated before the org's operational subdirectories are removed.

---

## Step 1 — Collect organization reference

Ask the operator:

```
To set up a unit, provide the parent organization context:

1. Program slug
2. Batch slug (if the org is inside a batch — leave blank if directly under program)
3. Organization slug
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate parent organization

Determine org path based on inputs:

- No batch → `programs/[program-slug]/orgs/[org-slug]/org-context.md`
- With batch → `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/org-context.md`

If not found:
```
Organization "[org-slug]" not found.
Run /vpm-setup-org to initialize it first.
```

Stop. Do not proceed if the parent is missing.

Read the org context file and extract:
- Organization name
- Mode (to inherit)
- `has_units` value
- Current `Has Units` status

---

## Step 3 — Check for existing artefacts (migration required)

Check whether the org's operational subdirectories contain any files:

- `challenges/*.md` — count files
- `solvers/*.md` — count files
- `pilots/*.md` — count files
- `blockers/*.md` — count files
- `decisions/*.md` — count files

If any files are found AND `has_units` is currently `false`, the org has artefacts that need to be migrated to the new unit. Notify the operator:

```
Migration required — [Organization Name] has existing artefacts

  Challenges:  [N] files
  Solvers:     [N] files
  Pilots:      [N] files
  Blockers:    [N] files
  Decisions:   [N] files

When this unit is created, these artefacts will be moved to the new unit's folder.
The organization's operational subdirectories will then be removed.

The org-context.md will be updated to: has_units: true

Do you want to proceed with migration? (YES / CANCEL)
```

Wait for the operator's confirmation before proceeding.

If the operator types CANCEL, stop without creating any files.

---

## Step 4 — Choose input mode

Ask the operator:

```
Setting up a unit under: [Organization Name]

How would you like to provide the unit information?

  A) Manual entry    — answer questions interactively
  B) Document import — attach or paste a document (PDF, Word, or plain text)
  C) Web search      — search verified public sources by team or unit name

Type A, B, or C.
```

Wait for the operator's response before proceeding.

---

## Step 5 — Collect unit data

### Mode A — Manual entry

Ask for the following in a single message:

```
Unit setup — manual entry

1. Unit name (e.g., "Operations Division", "Digital Innovation Team")
2. Function (what this unit is responsible for within the organization)
3. Team lead (named leader or owner of this unit)
4. Market position (role or standing of this unit — e.g., "largest division by revenue", "digital transformation arm", "regulatory-facing team")
5. Innovation signals (what this unit is already doing in innovation or technology, if known)
6. Culture (decision-making style, risk tolerance, openness to change)
7. Regulatory constraints (specific regulations or compliance requirements that affect this unit, if any)
8. Key stakeholders (who approves, who champions, who could block — names and roles if known)
9. Scope (geographies, product lines, or operational domains this unit covers)
10. Challenge domains (specific problem areas or innovation priorities this unit owns)
11. Budget (available for challenges or pilots, if known)
```

Mark any field not provided as `[TO CONFIRM]`.

### Mode B — Document import

Ask the operator to attach or paste the document:

```
Attach or paste the source document.
Accepted: PDF, Word (.docx), or plain text.
```

Read the document and extract all unit fields. Mark any field that cannot be confidently extracted as `[TO CONFIRM]`. Do not invent values.

Extraction map:

| Field | What to look for |
|---|---|
| `name` | Name of the business unit, division, team, or function |
| `function` | What this unit is responsible for within the organization |
| `team_lead` | Named leader or owner of this unit |
| `market_position` | Role, standing, or competitive position of this unit — stated directly or implied |
| `innovation_signals` | Innovation or technology initiatives already underway, mentioned in the document |
| `culture` | Decision-making style, risk tolerance, or openness to change if described |
| `regulatory_constraints` | Specific regulations, compliance requirements, or oversight bodies affecting this unit |
| `key_stakeholders` | Named decision-makers, champions, or potential blockers with their roles |
| `scope` | Geographies, product lines, or operational domains this unit covers |
| `challenge_domains` | Specific problem areas or innovation priorities explicitly stated |
| `budget` | Budget available for challenges or pilots, if stated |

### Mode C — Web search

Ask the operator:

```
Provide the unit or team name (and organization name if needed) to search:
```

Search verified public sources to extract unit fields. Mark any field not confirmed from public sources as `[TO CONFIRM]`. Do not invent values.

---

## Step 6 — Present preview for review

Display all collected data before creating any files:

```
Unit setup preview — [Unit Name]

  name:              [value or TO CONFIRM]
  type:              unit
  parent:            [org-slug]
  mode:              [inherited from [org-slug]]
  status:            active

  function:               [value or TO CONFIRM]
  team_lead:              [value or TO CONFIRM]
  market_position:        [value or TO CONFIRM]
  innovation_signals:     [value or TO CONFIRM]
  culture:                [value or TO CONFIRM]
  regulatory_constraints: [value or TO CONFIRM]
  key_stakeholders:       [value or TO CONFIRM]
  scope:                  [value or TO CONFIRM]
  challenge_domains:      [value or TO CONFIRM]
  budget:                 [value or TO CONFIRM]

  [If migration needed:]
  Artefacts to migrate: [N challenges, N solvers, N pilots, N blockers, N decisions]

Does this look correct?
  - Type YES to proceed.
  - Type EDIT followed by corrections to adjust.
  - Type CANCEL to stop without creating any files.
```

Wait for the operator's response. If EDIT, apply corrections and redisplay. Wait for YES before proceeding.

---

## Step 7 — Derive unit slug

Normalize the unit name: lowercase, hyphen-separated, no special characters, no accents.

Example: "Last Mile Operations" → `last-mile-operations`

Determine folder path based on org location:

- Org is under program → `programs/[program-slug]/orgs/[org-slug]/units/[unit-slug]/`
- Org is under batch → `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/units/[unit-slug]/`

If a folder already exists at that path, notify the operator:

```
A unit with slug "[unit-slug]" already exists under "[org-slug]".
Do you want to (A) overwrite the existing unit-context.md or (B) cancel?
```

---

## Step 8 — Create folder structure and generate unit-context.md

Create the unit folder and all operational subdirectories:

```
[org-folder]/
└── units/
    └── [unit-slug]/
        ├── unit-context.md
        ├── challenges/
        ├── solvers/
        ├── pilots/
        ├── blockers/
        └── decisions/
```

Use this template for `unit-context.md`:

```markdown
# [Unit Name] — Unit Context

## Identity

| Field | Value |
|---|---|
| Name | [Unit Name] |
| Slug | [unit-slug] |
| Type | unit |
| Parent | [org-slug] |
| Mode | inherited from [org-slug] |
| Status | active |
| Created | [YYYY-MM-DD] |

---

## Function

[What this unit is responsible for within the organization.]

---

## Team Lead

[Named leader or owner of this unit, or TO CONFIRM]

---

## Market Position

[Role or standing of this unit within the organization or its sector, or TO CONFIRM]

---

## Scope

| Field | Value |
|---|---|
| Geography | [Regions or TO CONFIRM] |
| Product lines | [Lines or TO CONFIRM] |
| Operational domains | [Domains or TO CONFIRM] |

---

## Innovation Signals

[What this unit is already doing in innovation or technology — ongoing initiatives, tools adopted, declared priorities, or TO CONFIRM]

---

## Culture

[Decision-making style, risk tolerance, and openness to change, or TO CONFIRM]

---

## Regulatory Constraints

[Specific regulations, compliance requirements, or oversight bodies affecting this unit, or none]

---

## Key Stakeholders

| Role | Name | Notes |
|---|---|---|
| Unit lead | [Name] | [Approver / Champion / TO CONFIRM] |
| Executive sponsor | [Name] | [TO CONFIRM] |

---

## Challenge Domains

[Specific problem areas or innovation priorities this unit owns, or TO CONFIRM]

---

## Budget

[Budget available for challenges or pilots, or TO CONFIRM]

---

## Fields to Confirm

- [ ] [Field] — [why it matters]

---

## Notes

[Any additional context.]
```

---

## Step 9 — Migrate artefacts (if applicable)

If the org had existing artefacts (identified in Step 3), move them to the new unit:

For each artefact file found in the org's operational subdirectories, move it to the corresponding subdirectory in the new unit folder.

```
Move: [org-folder]/challenges/[file].md → [unit-folder]/challenges/[file].md
Move: [org-folder]/solvers/[file].md    → [unit-folder]/solvers/[file].md
Move: [org-folder]/pilots/[file].md     → [unit-folder]/pilots/[file].md
Move: [org-folder]/blockers/[file].md   → [unit-folder]/blockers/[file].md
Move: [org-folder]/decisions/[file].md  → [unit-folder]/decisions/[file].md
```

After moving all files, delete the org's now-empty operational subdirectories:
- `[org-folder]/challenges/`
- `[org-folder]/solvers/`
- `[org-folder]/pilots/`
- `[org-folder]/blockers/`
- `[org-folder]/decisions/`

Then update `org-context.md`: change `Has Units` from `false` to `true`.

---

## Step 10 — Confirm and suggest next step

```
Unit created — [Unit Name]

  Slug:    [unit-slug]
  Parent:  [org-slug]
  Mode:    inherited
  Status:  active

  Fields confirmed:   [N]
  Fields to confirm:  [N]

Files created:
  [unit-folder]/unit-context.md
  [unit-folder]/challenges/
  [unit-folder]/solvers/
  [unit-folder]/pilots/
  [unit-folder]/blockers/
  [unit-folder]/decisions/

  [If artefacts were migrated:]
  Artefacts migrated from [org-slug]:
    [N] challenges, [N] solvers, [N] pilots, [N] blockers, [N] decisions
  Organization updated: has_units → true
  Organization operational subdirs removed.

Next step:
  Run /vpm-challenge-workshop to define challenges for this unit.
```

---

## Quality criteria

- [ ] Parent organization was validated before data collection began.
- [ ] Existing artefacts were detected and migration was confirmed before proceeding.
- [ ] Input mode was selected before data collection began.
- [ ] All fields not provided or found were marked [TO CONFIRM] — not invented.
- [ ] Operator reviewed and approved the preview before any file was created.
- [ ] All six operational subdirectories were created under the unit.
- [ ] If migration occurred: all artefact files moved, org subdirs deleted, org-context.md updated to `has_units: true`.
- [ ] Slug: lowercase, hyphen-separated, no accents.
- [ ] Folder path is nested correctly under the org's `units/` folder.
- [ ] Confirmation summary shows confirmed and unconfirmed field counts.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-setup-org` | Organization must exist before a unit can be created. |
| Followed by | `/vpm-challenge-workshop` | Define and prioritize challenges for this unit. |
| Followed by | `/vpm-init` | Re-run to see the updated dashboard. |

# /vpm-setup-program

Initializes a new entity in VenturePM by collecting details, validating the hierarchy, creating the folder structure, and generating `program-context.md`. Supports all four entity types: program, batch, organization, and unit.

---

## Step 1 — Collect entity details

Ask the operator for all of the following in a single message:

```
To set up a new entity, please provide the following:

1. Entity type
   What kind of entity is this?
   Options: program / batch / organization / unit

   Hierarchy reference:
     program      → top-level initiative, no parent
     batch        → cohort of a program (parent: program)
     organization → company or entity in a batch or program
     unit         → sub-team within an organization

2. Entity name
   What is the name of this entity?

3. Parent slug
   The slug of the parent entity in the hierarchy.
   Use null if type is program.
   Example: global-innovation-sprint

4. Mode
   venture-client / venture-building / hybrid
   For batch, organization, and unit: leave blank to inherit from parent.

5. Description
   What is the purpose or role of this entity? (1–2 sentences)

6. Process intensity (programs and batches only)
   light / standard / full
   - light: simplified pipeline, fewer gates, smaller team
   - standard: full 5-phase pipeline with standard governance
   - full: extended pipeline with additional evaluation layers and reporting
   Leave blank for organization and unit.

7. Lead
   Name and contact (email or handle) of the person responsible for this entity.

8. Start date and expected duration
   Example: 2026-04-01, 6 months
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate entity type and parent

Apply the following rules based on the declared entity type:

### If type = `program`
- `parent` must be `null`. If the operator provided a parent slug, ask them to confirm this is a top-level entity before proceeding.
- `mode` is required. If not provided, ask for it before continuing.

### If type = `batch`
- `parent` is required and must be of type `program`.
- Check that `programs/[parent-slug]/program-context.md` exists and that its `type` field is `program`.
- If not found or type mismatch, stop:
  ```
  Parent entity "[parent-slug]" not found or is not a program.
  A batch must be parented to a program. Run /vpm-setup-program with type: program first.
  ```
- `mode` is inherited from the parent program unless the operator explicitly provides an override.

### If type = `organization`
- `parent` is required and must be of type `program` or `batch`.
- Check that `programs/[parent-slug]/program-context.md` exists and that its `type` field is `program` or `batch`.
- If not found or type mismatch, stop:
  ```
  Parent entity "[parent-slug]" not found or is not a program or batch.
  An organization must be parented to a program or batch.
  ```
- `mode` is inherited from the parent. Do not ask for it.

### If type = `unit`
- `parent` is required and must be of type `organization`.
- Check that `programs/[parent-slug]/program-context.md` exists and that its `type` field is `organization`.
- If not found or type mismatch, stop:
  ```
  Parent entity "[parent-slug]" not found or is not an organization.
  A unit must be parented to an organization.
  ```
- `mode` is inherited from the parent organization. Do not ask for it.

---

## Step 3 — Derive the entity slug

Convert the entity name to a slug: lowercase, hyphen-separated, no special characters, no accents.

Example: "Acme Logistics Corp" → `acme-logistics-corp`
Example: "Cohort 2026-Q1" → `cohort-2026-q1`

If a folder already exists at `programs/[entity-slug]/`, notify the operator:
```
An entity with slug "[entity-slug]" already exists.
Do you want to (A) choose a different name or (B) overwrite the existing program-context.md?
```

Wait for the operator's response before proceeding.

---

## Step 4 — Create the folder structure

Create the following directories:

```
programs/[entity-slug]/
├── program-context.md
├── challenges/
├── solvers/
├── pilots/
├── blockers/
└── decisions/
```

Subfolders are created empty. Do not add placeholder files inside them.

---

## Step 5 — Generate program-context.md

Create `programs/[entity-slug]/program-context.md` using the appropriate template for the entity type.

### Template — program

```markdown
# [Entity Name] — Program Context

## Identity

| Field | Value |
|---|---|
| Name | [Entity Name] |
| Slug | [entity-slug] |
| Type | program |
| Parent | null |
| Mode | [venture-client / venture-building / hybrid] |
| Status | active |

## Description

[Description]

## Process Configuration

| Field | Value |
|---|---|
| Intensity | [light / standard / full] |
| Pipeline phases | Setup → Challenges → Scouting → Evaluation → Pilots |
| Blocker tracking | enabled |
| Decision log | enabled |

## Team

| Role | Name | Contact |
|---|---|---|
| Program Lead | [Name] | [Contact] |

## Batches / Organizations

_No batches or organizations enrolled yet._
_Run /vpm-setup-program (type: batch or organization) or /vpm-org-setup to add the first._

## Success Metrics

_To be defined._

## Key Dates

| Milestone | Date |
|---|---|
| Start date | [Start date] |
| Expected end date | [Calculated from start + duration] |
| Last updated | [Today's date — YYYY-MM-DD] |
```

---

### Template — batch

```markdown
# [Entity Name] — Batch Context

## Identity

| Field | Value |
|---|---|
| Name | [Entity Name] |
| Slug | [entity-slug] |
| Type | batch |
| Parent | [parent-slug] |
| Mode | [inherited from parent / override if specified] |
| Status | active |

## Description

[Description]

## Process Configuration

| Field | Value |
|---|---|
| Intensity | [light / standard / full] |
| Pipeline phases | Setup → Challenges → Scouting → Evaluation → Pilots |
| Blocker tracking | enabled |
| Decision log | enabled |

## Team

| Role | Name | Contact |
|---|---|---|
| Batch Lead | [Name] | [Contact] |

## Organizations Enrolled

_No organizations enrolled yet. Run /vpm-org-setup to add the first._

## Key Dates

| Milestone | Date |
|---|---|
| Start date | [Start date] |
| Expected end date | [Calculated from start + duration] |
| Last updated | [Today's date — YYYY-MM-DD] |
```

---

### Template — organization

```markdown
# [Entity Name] — Organization Context

## Identity

| Field | Value |
|---|---|
| Name | [Entity Name] |
| Slug | [entity-slug] |
| Type | organization |
| Parent | [parent-slug] |
| Mode | [inherited from parent] |
| Status | active |

## Description

[Description — what this organization does and its role in the program.]

## Profile

| Field | Value |
|---|---|
| Industry | _To be added_ |
| Headquarters | _To be added_ |
| Size | _To be added_ |
| Website | _To be added_ |

## Team

| Role | Name | Contact |
|---|---|---|
| Organization Lead | [Name] | [Contact] |

## Units

_No units defined yet. Run /vpm-setup-program (type: unit) if sub-team scoping is needed._

## Challenges

_No challenges defined yet. Run /vpm-challenge-workshop to identify and prioritize challenges._

## Key Dates

| Milestone | Date |
|---|---|
| Enrolled | [Today's date — YYYY-MM-DD] |
| Last updated | [Today's date — YYYY-MM-DD] |
```

---

### Template — unit

```markdown
# [Entity Name] — Unit Context

## Identity

| Field | Value |
|---|---|
| Name | [Entity Name] |
| Slug | [entity-slug] |
| Type | unit |
| Parent | [parent-slug] |
| Mode | [inherited from parent] |
| Status | active |

## Description

[Description — what this unit is responsible for and its role within the parent organization.]

## Team

| Role | Name | Contact |
|---|---|---|
| Unit Lead | [Name] | [Contact] |

## Challenges

_No challenges defined yet. Run /vpm-challenge-workshop to identify and prioritize challenges._

## Key Dates

| Milestone | Date |
|---|---|
| Enrolled | [Today's date — YYYY-MM-DD] |
| Last updated | [Today's date — YYYY-MM-DD] |
```

---

## Step 6 — Confirm and suggest next step

Display a confirmation summary:

```
Entity created successfully.

  Name:    [Entity Name]
  Slug:    [entity-slug]
  Type:    [entity type]
  Parent:  [parent-slug or none]
  Mode:    [mode or inherited from parent-slug]
  Lead:    [Lead name]
  Start:   [Start date]

Files created:
  programs/[entity-slug]/program-context.md
  programs/[entity-slug]/challenges/
  programs/[entity-slug]/solvers/
  programs/[entity-slug]/pilots/
  programs/[entity-slug]/blockers/
  programs/[entity-slug]/decisions/
```

Suggest next step based on entity type:

| Entity type | Next step |
|---|---|
| `program` | Run `/vpm-setup-program` again with type `batch` to add a cohort, `/vpm-org-setup` to enroll organizations directly, or `/vpm-import-program` to load entities from existing documents. |
| `batch` | Run `/vpm-org-setup` to enroll the first organization into this batch. |
| `organization` | Run `/vpm-challenge-workshop` to identify and prioritize challenges for this organization. |
| `unit` | Run `/vpm-challenge-workshop` to identify and prioritize challenges for this unit. |

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Alternative to | `/vpm-import-program` | Use `/vpm-import-program` when an existing document describes the entity — avoids re-entering information manually. |
| Preceded by | `/vpm-setup-program` (parent entity) | Build the hierarchy top-down: create program first, then batch, then organization, then unit. |
| Followed by | `/vpm-org-setup` | After creating a program or batch, use this to enroll organizations with a full profile. |
| Followed by | `/vpm-challenge-workshop` | After creating an organization or unit, define and prioritize challenges. |

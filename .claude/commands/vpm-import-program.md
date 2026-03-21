# /vpm-import-program

Loads program context from an existing document (PDF, Word, or plain text) and generates the appropriate entity file with all extractable fields pre-filled. Supports all four entity types: program, batch, organization, and unit. An alternative entry point to `/vpm-setup-program` for operators who already have documentation.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To import a program entity from a document, please provide:

1. Document
   Attach or paste the source document.
   Accepted formats: PDF, Word (.docx), plain text, or pasted content.
   This can be a program brief, company overview, accelerator dossier,
   investment memo, or any document describing the entity.

2. Entity type
   What kind of entity does this document describe?
   Options: program / batch / organization / unit

3. Parent slug
   The slug of the parent entity in the hierarchy.
   Use null if this is a top-level program.
   Example: global-innovation-sprint

   Hierarchy reference:
     program   → parent: null
     batch     → parent: [program-slug]
     organization → parent: [batch-slug] or [program-slug]
     unit      → parent: [organization-slug]
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate parent entity

If `parent` is not `null`:

Check that `programs/[parent-slug]/program-context.md` exists.

If not found:
```
Parent entity "[parent-slug]" not found.

The parent must be initialized before importing a child entity.
Run /vpm-import-program or /vpm-setup-program for the parent first.
```

Stop. Do not proceed if the parent is missing.

If the parent exists, read its `program-context.md` and extract:
- Parent name and type
- Mode (to inherit as default for this entity)

Validate that the declared entity type is consistent with the hierarchy:
- `batch` → parent must be of type `program`
- `organization` → parent must be of type `program` or `batch`
- `unit` → parent must be of type `organization`

If the relationship is invalid, stop and explain the expected hierarchy.

---

## Step 3 — Extract structured information from the document

Read the provided document and extract all fields relevant to the declared entity type. Use the extraction maps below.

Mark any field that cannot be confidently extracted as `[TO CONFIRM]`.

Do not invent values. Do not infer specifics from generic language (e.g., if the document says "we focus on innovation" without naming a mode, mark `mode` as `[TO CONFIRM]`).

### Extraction map — program

| Field | What to look for in the document |
|---|---|
| `name` | Official name of the initiative or program |
| `mode` | Whether the entity buys from startups (venture-client), builds internally (venture-building), or does both (hybrid) |
| `description` | Program purpose, mission, or strategic intent |
| `start_date` | Launch or kickoff date |
| `end_date` | Planned end or review date |
| `team` | Named team members and their roles |
| `budget` | Total or annual budget for the program |
| `success_metrics` | KPIs, targets, or evaluation criteria stated in the document |
| `governance` | Committees, sponsors, or approval structures |
| `scope` | Industries, geographies, or technology domains in focus |

### Extraction map — batch

| Field | What to look for in the document |
|---|---|
| `name` | Cohort name, edition, or cycle identifier |
| `mode` | Override mode if different from parent program; otherwise inherit |
| `period` | Start and end dates for this cohort |
| `conditions` | Rules, criteria, or constraints specific to this batch |
| `budget` | Budget allocated to this cohort |
| `team` | Cohort-specific team members or mentors |
| `focus_areas` | Themes, sectors, or challenges this cohort addresses |
| `participant_count` | Number of organizations expected or confirmed |

### Extraction map — organization

| Field | What to look for in the document |
|---|---|
| `name` | Legal or trading name of the organization |
| `website` | Official URL |
| `founded` | Year founded |
| `headquarters` | City and country |
| `size` | Team size or employee range |
| `funding_stage` | Bootstrapped / Seed / Series A / Series B+ / Public / Unknown |
| `industry` | Primary sector or domain |
| `description` | What the organization does and the value it delivers |
| `key_contacts` | Named contacts with roles |
| `challenge_domains` | Areas of interest or stated problem areas |
| `prior_relationship` | Any existing relationship with the program or parent entity |

### Extraction map — unit

| Field | What to look for in the document |
|---|---|
| `name` | Name of the business unit, team, or function |
| `function` | What this unit is responsible for within the organization |
| `team_lead` | Named leader or owner of this unit |
| `scope` | Geographies, product lines, or operational domains this unit covers |
| `challenge_domains` | Problem areas or innovation priorities this unit owns |
| `budget` | Budget available for challenges or pilots, if stated |

---

## Step 4 — Present extraction summary for review

Before creating any files, display the extracted data for operator review:

```
Import preview — [Entity type]: [Extracted name or "[TO CONFIRM]"]

The following fields were extracted from the document.
Fields marked [TO CONFIRM] could not be confidently determined.

  name:             [value or TO CONFIRM]
  type:             [entity type]
  parent:           [parent-slug or null]
  mode:             [value or TO CONFIRM (inherited from [parent-slug])]

  [Additional fields per entity type — see extraction maps]

Does this look correct?
  - Type YES to proceed with file creation.
  - Type EDIT followed by corrections to adjust before proceeding.
  - Type CANCEL to stop without creating any files.
```

Wait for the operator's response before proceeding.

If the operator provides corrections (EDIT), apply them and display the updated preview. Wait for a final YES before proceeding.

---

## Step 5 — Derive entity slug

Normalize the entity name: lowercase, hyphen-separated, no special characters, no accents.

Example: "Acme Logistics Corp" → `acme-logistics-corp`

Entity slug: `[derived-slug]`
Folder path: `programs/[entity-slug]/`

If a folder already exists at that path, notify the operator:

```
A folder already exists for "[entity-slug]".

Do you want to (A) overwrite the existing program-context.md or (B) cancel?
```

Wait for the operator's response.

---

## Step 6 — Create folder structure

Create the following directories:

```
programs/[entity-slug]/
programs/[entity-slug]/challenges/
programs/[entity-slug]/solvers/
programs/[entity-slug]/pilots/
programs/[entity-slug]/blockers/
programs/[entity-slug]/decisions/
```

---

## Step 7 — Generate program-context.md

Create `programs/[entity-slug]/program-context.md` using the template below.

Pre-fill all confirmed fields. Write `[TO CONFIRM]` for any field that could not be extracted or was not corrected by the operator.

---

## Step 8 — Confirm and suggest next step

```
Import complete — [Entity type]: [Entity name]

  Slug:    [entity-slug]
  Type:    [entity type]
  Parent:  [parent-slug or none]
  Mode:    [mode or inherited from parent]

  Fields confirmed:   [N]
  Fields to confirm:  [N marked as TO CONFIRM]

Files created:
  programs/[entity-slug]/program-context.md
  programs/[entity-slug]/challenges/
  programs/[entity-slug]/solvers/
  programs/[entity-slug]/pilots/
  programs/[entity-slug]/blockers/
  programs/[entity-slug]/decisions/

[If fields are marked TO CONFIRM:]
  Review programs/[entity-slug]/program-context.md and fill in all [TO CONFIRM] fields
  before running the next command.
```

Suggest next step based on entity type:

| Entity type imported | Suggested next step |
|---|---|
| `program` | Run `/vpm-import-program` again with type `batch` to add a cohort, or `/vpm-org-setup` to onboard organizations directly. |
| `batch` | Run `/vpm-org-setup` to onboard organizations into this batch. |
| `organization` | Run `/vpm-challenge-workshop` to identify and prioritize challenges for this organization. |
| `unit` | Run `/vpm-challenge-workshop` to identify and prioritize challenges for this unit. |

---

## Output template

```markdown
# [Entity Name] — Context

## Identity

| Field | Value |
|---|---|
| Name | [Entity name] |
| Slug | [entity-slug] |
| Type | [program / batch / organization / unit] |
| Parent | [parent-slug or null] |
| Mode | [venture-client / venture-building / hybrid / inherited] |
| Imported | [YYYY-MM-DD] |
| Source document | [Document name or description] |

---

## Description

[Extracted description of the entity — purpose, mission, or strategic intent. Mark as [TO CONFIRM] if not found.]

---

## [Section A — varies by entity type]

[For program: Team, Budget, Success Metrics, Governance, Scope]
[For batch: Period, Conditions, Focus Areas, Participant Count]
[For organization: Website, Founded, HQ, Size, Funding Stage, Industry, Key Contacts, Challenge Domains]
[For unit: Function, Team Lead, Scope, Challenge Domains]

---

## Fields to confirm

The following fields could not be extracted from the source document and must be filled in manually:

- [ ] [Field name] — [why it matters]
- [ ] [Field name] — [why it matters]

---

## Notes

[Any additional context from the document that does not fit the structured fields above.]
```

---

## Quality criteria

- [ ] Parent entity was validated before extraction began.
- [ ] Entity type hierarchy consistency was checked (e.g., batch cannot be child of organization).
- [ ] Extraction used only information present in the document — no invented values.
- [ ] Fields that could not be extracted were marked `[TO CONFIRM]`, not guessed.
- [ ] Operator reviewed and approved the extraction before any file was created.
- [ ] Folder structure was created for all six subdirectories.
- [ ] Next step suggestion matched the entity type imported.
- [ ] Confirmation summary listed both confirmed and unconfirmed field counts.

---

## Usage example

```
Operator: /vpm-import-program

Claude: To import a program entity from a document, please provide:
        1. Document (attach or paste)
        2. Entity type
        3. Parent slug

Operator: [attaches PDF]
          type: organization
          parent: cohort-2026-q1

Claude: [Reads PDF, validates parent exists, extracts org fields,
         displays preview with [TO CONFIRM] markers, waits for approval,
         creates folder structure, generates program-context.md,
         suggests /vpm-challenge-workshop as next step]
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Alternative to | `/vpm-setup-program` | Use when an existing document describes the entity — avoids re-entering information manually. |
| Preceded by | `/vpm-import-program` (parent) | Build the hierarchy top-down: import program first, then batch, then organization, then unit. |
| Followed by | `/vpm-org-setup` | After importing a program or batch, onboard organizations. |
| Followed by | `/vpm-challenge-workshop` | After importing an organization or unit, define and prioritize challenges. |

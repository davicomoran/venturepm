# /vpm-setup-batch

Sets up a new batch (cohort) inside an existing program. Supports three input modes: manual entry, document import, or web search from verified sources. A batch is a context container — only `batch-context.md` is created, no operational subdirectories.

---

## Step 1 — Collect program slug

Ask the operator:

```
To set up a batch, provide the slug of the parent program:

  Program slug: [e.g., global-innovation-sprint]
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate parent program

Check that `programs/[program-slug]/program-context.md` exists.

Read it and confirm `type: program`.

If not found or wrong type:
```
Program "[program-slug]" not found.
Run /vpm-setup-program to initialize it first.
```

Stop. Do not proceed if the parent is missing.

Extract from the parent context file:
- Program name
- Mode (to inherit as default for the batch)

---

## Step 3 — Choose input mode

Ask the operator:

```
Setting up a batch for: [Program Name]

How would you like to provide the batch information?

  A) Manual entry    — answer questions interactively
  B) Document import — attach or paste a document (PDF, Word, or plain text)
  C) Web search      — search verified public sources by batch or program name

Type A, B, or C.
```

Wait for the operator's response before proceeding.

---

## Step 4 — Collect batch data

### Mode A — Manual entry

Ask for the following in a single message:

```
Batch setup — manual entry

1. Batch name (e.g., "Cohort 2026-Q1" or "Spring Edition")
2. Mode: venture-client
   (leave blank to inherit from parent program: [inherited mode])
3. Description (1–3 sentences — purpose or focus of this cohort)
4. Start date (YYYY-MM-DD or approximate)
5. End date (YYYY-MM-DD or approximate)
6. Focus areas (themes, sectors, or challenges this cohort addresses)
7. Participant count (expected or confirmed number of organizations)
8. Conditions (rules, criteria, or constraints specific to this batch)
9. Budget (amount allocated to this cohort, if known)
10. Team (cohort-specific team members or mentors, if known)
```

Mark any field not provided as `[TO CONFIRM]`.

### Mode B — Document import

Ask the operator to attach or paste the document:

```
Attach or paste the source document.
Accepted: PDF, Word (.docx), or plain text.
```

Read the document and extract all batch fields. Mark any field that cannot be confidently extracted as `[TO CONFIRM]`. Do not invent values.

Extraction map:

| Field | What to look for |
|---|---|
| `name` | Cohort name, edition, or cycle identifier |
| `mode` | Override mode if different from parent; otherwise inherit |
| `description` | Cohort purpose, focus, or strategic intent |
| `period` | Start and end dates for this cohort |
| `focus_areas` | Themes, sectors, or challenge domains |
| `participant_count` | Number of organizations expected or confirmed |
| `conditions` | Rules or criteria specific to this batch |
| `budget` | Budget allocated to this cohort |
| `team` | Cohort-specific team members or mentors |

### Mode C — Web search

Ask the operator:

```
Provide the batch or program name to search:
```

Search verified public sources (official website, LinkedIn, press releases, news) to extract batch fields. Mark any field not confirmed from public sources as `[TO CONFIRM]`. Do not invent values.

---

## Step 5 — Present preview for review

Display all collected data before creating any files:

```
Batch setup preview — [Batch Name]

  name:              [value or TO CONFIRM]
  type:              batch
  parent:            [program-slug]
  mode:              [value or "inherited from [program-slug]"]
  status:            active

  description:       [value or TO CONFIRM]
  start_date:        [value or TO CONFIRM]
  end_date:          [value or TO CONFIRM]
  focus_areas:       [value or TO CONFIRM]
  participant_count: [value or TO CONFIRM]
  conditions:        [value or TO CONFIRM]
  budget:            [value or TO CONFIRM]
  team:              [value or TO CONFIRM]

Does this look correct?
  - Type YES to proceed.
  - Type EDIT followed by corrections to adjust.
  - Type CANCEL to stop without creating any files.
```

Wait for the operator's response. If EDIT, apply corrections and redisplay. Wait for YES before proceeding.

---

## Step 6 — Derive batch slug

Normalize the batch name: lowercase, hyphen-separated, no special characters, no accents.

Example: "Cohort 2026-Q1" → `cohort-2026-q1`

Folder path: `programs/[program-slug]/batches/[batch-slug]/`

If a folder already exists at that path, notify the operator:

```
A batch with slug "[batch-slug]" already exists under "[program-slug]".
Do you want to (A) overwrite the existing batch-context.md or (B) cancel?
```

Wait for the operator's response.

---

## Step 7 — Create folder and generate batch-context.md

Create `programs/[program-slug]/batches/[batch-slug]/` with only the context file. No operational subdirectories.

```
programs/[program-slug]/
└── batches/
    └── [batch-slug]/
        └── batch-context.md
```

Use this template:

```markdown
# [Batch Name] — Batch Context

## Identity

| Field | Value |
|---|---|
| Name | [Batch Name] |
| Slug | [batch-slug] |
| Type | batch |
| Parent | [program-slug] |
| Mode | [venture-client / inherited from [program-slug]] |
| Status | active |
| Created | [YYYY-MM-DD] |

---

## Description

[Batch purpose, focus, or strategic intent.]

---

## Period

| Milestone | Date |
|---|---|
| Start date | [YYYY-MM-DD or TO CONFIRM] |
| End date | [YYYY-MM-DD or TO CONFIRM] |
| Last updated | [Today's date — YYYY-MM-DD] |

---

## Focus Areas

[Themes, sectors, or challenge domains this cohort addresses, or TO CONFIRM]

---

## Conditions

[Rules, criteria, or constraints specific to this batch, or TO CONFIRM]

---

## Participants

| Field | Value |
|---|---|
| Expected organizations | [N or TO CONFIRM] |

---

## Budget

[Budget allocated to this cohort, or TO CONFIRM]

---

## Team

| Role | Name | Contact |
|---|---|---|
| Batch Lead | [Name] | [Contact] |

---

## Fields to Confirm

- [ ] [Field] — [why it matters]
```

---

## Step 8 — Confirm and suggest next step

```
Batch created — [Batch Name]

  Slug:    [batch-slug]
  Parent:  [program-slug]
  Mode:    [mode or inherited]
  Status:  active

  Fields confirmed:   [N]
  Fields to confirm:  [N]

Files created:
  programs/[program-slug]/batches/[batch-slug]/batch-context.md

Next step:
  Run /vpm-setup-org to onboard organizations into this batch.
```

---

## Quality criteria

- [ ] Parent program was validated before data collection began.
- [ ] Input mode was selected before data collection began.
- [ ] All fields not provided or found were marked [TO CONFIRM] — not invented.
- [ ] Operator reviewed and approved the preview before any file was created.
- [ ] Only batch-context.md was created — no operational subdirectories.
- [ ] Slug: lowercase, hyphen-separated, no accents.
- [ ] Folder path: `programs/[program-slug]/batches/[batch-slug]/`.
- [ ] Confirmation summary shows confirmed and unconfirmed field counts.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-setup-program` | A program must exist before a batch can be created. |
| Followed by | `/vpm-setup-org` | Add organizations to this batch. |
| Followed by | `/vpm-init` | Re-run to see the updated dashboard. |

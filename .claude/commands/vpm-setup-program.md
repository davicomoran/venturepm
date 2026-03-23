# /vpm-setup-program

Sets up a new program in VenturePM. Supports three input modes: manual entry, document import, or web search from verified sources. Programs are context containers — only `program-context.md` is created, no operational subdirectories.

---

## Step 1 — Choose input mode

Ask the operator:

```
To set up a new program, how would you like to provide the information?

  A) Manual entry    — answer questions interactively
  B) Document import — attach or paste a document (PDF, Word, or plain text)
  C) Web search      — search verified public sources by program or organization name

Type A, B, or C.
```

Wait for the operator's response before proceeding.

---

## Step 2 — Collect program data

### Mode A — Manual entry

Ask for the following in a single message:

```
Program setup — manual entry

1.  Program name
2.  Mode: venture-client
3.  Description (1–3 sentences — purpose or mission)
4.  Start date (YYYY-MM-DD or approximate)
5.  End date (optional)
6.  Team (names and roles, if known)
7.  Budget (total or annual, if known)
8.  Success metrics (KPIs or targets, if defined)
9.  Governance (committees, sponsors, approval structures)
10. Scope (geography, industries, or technology domains)
11. Solver criteria (eligibility rules for who can participate as a solver — leave blank to use VenturePM defaults)
12. Evaluation process (custom phases or gates — leave blank to use the standard 5-phase pipeline)
```

Mark any field not provided as `[TO CONFIRM]`.

### Mode B — Document import

Ask the operator to attach or paste the document:

```
Attach or paste the source document.
Accepted: PDF, Word (.docx), or plain text.
```

Read the document and extract all program fields. Mark any field that cannot be confidently extracted as `[TO CONFIRM]`. Do not invent values.

Extraction map:

| Field | What to look for |
|---|---|
| `name` | Official name of the initiative or program |
| `mode` | How the program works — currently: venture-client (organization sources and pilots solutions from external startups) |
| `description` | Program purpose, mission, or strategic intent |
| `start_date` | Launch or kickoff date |
| `end_date` | Planned end or review date |
| `team` | Named team members and their roles |
| `budget` | Total or annual budget for the program |
| `success_metrics` | KPIs, targets, or evaluation criteria stated in the document |
| `governance` | Committees, sponsors, or approval structures |
| `scope` | Industries, geographies, or technology domains in focus |
| `solver_criteria` | Eligibility rules for solvers — company type, size, geography, stage, or technology requirements explicitly stated |
| `evaluation_process` | Custom phases, gates, or evaluation methodology described in the document |

### Mode C — Web search

Ask the operator:

```
Provide the program or organization name to search:
```

Search verified public sources (official website, LinkedIn, Crunchbase, press releases, news) to extract program fields. Mark any field not confirmed from public sources as `[TO CONFIRM]`. Do not invent values.

---

## Step 3 — Present preview for review

Display all collected data before creating any files:

```
Program setup preview — [Program Name]

  name:             [value or TO CONFIRM]
  type:             program
  parent:           null
  mode:             [value or TO CONFIRM]
  status:           active

  description:        [value or TO CONFIRM]
  start_date:         [value or TO CONFIRM]
  end_date:           [value or TO CONFIRM]
  team:               [value or TO CONFIRM]
  budget:             [value or TO CONFIRM]
  success_metrics:    [value or TO CONFIRM]
  governance:         [value or TO CONFIRM]
  scope:              [value or TO CONFIRM]
  solver_criteria:    [value or "VenturePM defaults"]
  evaluation_process: [value or "Standard 5-phase pipeline"]

Does this look correct?
  - Type YES to proceed.
  - Type EDIT followed by corrections to adjust.
  - Type CANCEL to stop without creating any files.
```

Wait for the operator's response. If EDIT, apply corrections and redisplay. Wait for YES before proceeding.

---

## Step 4 — Derive program slug

Normalize the program name: lowercase, hyphen-separated, no special characters, no accents.

Example: "Global Innovation Sprint" → `global-innovation-sprint`

If a folder already exists at `programs/[program-slug]/`, notify the operator:

```
A program with slug "[program-slug]" already exists.
Do you want to (A) overwrite the existing program-context.md or (B) cancel?
```

Wait for the operator's response.

---

## Step 5 — Create folder and generate program-context.md

Create `programs/[program-slug]/` with only the context file. No subdirectories.

```
programs/[program-slug]/
└── program-context.md
```

Use this template:

```markdown
# [Program Name] — Program Context

## Identity

| Field | Value |
|---|---|
| Name | [Program Name] |
| Slug | [program-slug] |
| Type | program |
| Parent | null |
| Mode | venture-client |
| Status | active |
| Created | [YYYY-MM-DD] |

---

## Description

[Program purpose, mission, or strategic intent.]

---

## Team

| Role | Name | Contact |
|---|---|---|
| Program Lead | [Name] | [Contact] |

---

## Budget

[Budget details or TO CONFIRM]

---

## Success Metrics

[KPIs or targets or TO CONFIRM]

---

## Governance

[Committees, sponsors, or approval structures or TO CONFIRM]

---

## Scope

| Field | Value |
|---|---|
| Geography | [Regions or countries] |
| Domains | [Industries, sectors, or technology areas] |

---

## Solver Criteria

[Eligibility rules for who can participate as a solver — company type, size, geography, stage, technology requirements, or other constraints. If not defined, VenturePM defaults apply.]

---

## Evaluation Process

[Custom phases, gates, or evaluation methodology for this program. If not defined, the standard 5-phase pipeline applies: Setup → Challenges → Scouting → Evaluation → Pilots.]

---

## Key Dates

| Milestone | Date |
|---|---|
| Start date | [YYYY-MM-DD or TO CONFIRM] |
| End date | [YYYY-MM-DD or TO CONFIRM] |
| Last updated | [Today's date — YYYY-MM-DD] |

---

## Fields to Confirm

- [ ] [Field] — [why it matters]
```

---

## Step 6 — Confirm and suggest next step

```
Program created — [Program Name]

  Slug:    [program-slug]
  Mode:    [mode]
  Status:  active

  Fields confirmed:   [N]
  Fields to confirm:  [N]

Files created:
  programs/[program-slug]/program-context.md

Next step:
  Run /vpm-setup-batch to add a cohort, or
  Run /vpm-setup-org to onboard organizations directly.
```

---

## Quality criteria

- [ ] Input mode was selected before data collection began.
- [ ] All fields not provided or found were marked [TO CONFIRM] — not invented.
- [ ] Operator reviewed and approved the preview before any file was created.
- [ ] Only program-context.md was created — no operational subdirectories.
- [ ] Slug: lowercase, hyphen-separated, no accents.
- [ ] Confirmation summary shows confirmed and unconfirmed field counts.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Followed by | `/vpm-setup-batch` | Add a cohort to the program. |
| Followed by | `/vpm-setup-org` | Onboard organizations directly if no batches are needed. |
| Followed by | `/vpm-init` | Run after setup to see the updated dashboard. |

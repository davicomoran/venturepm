# /vpm-setup-org

Onboards a new organization into an active program or batch. Supports three input modes: manual entry, document import, or web search from verified sources. Creates the organization folder with all operational subdirectories. Organizations are challengers — they own challenges, solvers, pilots, blockers, and decisions.

---

## Step 1 — Collect parent reference

Ask the operator:

```
To set up an organization, provide the parent context:

1. Parent type: program / batch
2. Parent slug(s):
   - If parent is a program: provide the program slug
     Example: global-innovation-sprint
   - If parent is a batch: provide the program slug AND the batch slug
     Example: program: global-innovation-sprint / batch: cohort-2026-q1
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate parent entity

Determine the parent path based on declared type:

- Parent is `program` → `programs/[program-slug]/program-context.md`
- Parent is `batch` → `programs/[program-slug]/batches/[batch-slug]/batch-context.md`

If not found:
```
Parent entity "[slug]" not found.
Run /vpm-setup-program or /vpm-setup-batch to initialize it first.
```

Stop. Do not proceed if the parent is missing.

Read the parent context file and extract:
- Parent name and type
- Mode (to inherit as default for this organization)

---

## Step 3 — Choose input mode

Ask the operator:

```
Setting up an organization under: [Parent Name]

How would you like to provide the organization information?

  A) Manual entry    — answer questions interactively
  B) Document import — attach or paste a document (PDF, Word, or plain text)
  C) Web search      — search verified public sources by organization name

Type A, B, or C.
```

Wait for the operator's response before proceeding.

---

## Step 4 — Collect organization data

### Mode A — Manual entry

Ask for the following in a single message:

```
Organization setup — manual entry

1.  Organization name (legal or trading name)
2.  Entity type — what kind of organization is this?
    Examples: startup / corporation / holding / cooperative / public company / NGO / government / other
3.  Website (official URL)
4.  Founded (year)
5.  Headquarters (city, country)
6.  Size (team size or employee range — e.g., 50–200)
7.  Revenue scale (annual revenue or financial scale, if known — e.g., "$5M ARR", "~$2B revenue")
8.  Industry (primary sector or domain)
9.  Description (1–3 sentences — what the organization does and the value it delivers)
10. Strategic priorities (2–3 current priorities or focus areas, if known)
11. Culture (decision-making style, risk tolerance, openness to change — if known)
12. Regulatory environment (key regulations or oversight bodies that affect them, if any)
13. Key contacts (names and roles)
14. Challenge domains (specific problem areas or pain points this org brings to the program)
15. Prior relationship (any existing relationship with the program or parent entity)
16. Has units? (yes / no — whether this org will be divided into sub-entities)
```

Mark any field not provided as `[TO CONFIRM]`.

### Mode B — Document import

Ask the operator to attach or paste the document:

```
Attach or paste the source document.
Accepted: PDF, Word (.docx), or plain text.
This can be a company overview, investment memo, accelerator dossier, or any document describing the organization.
```

Read the document and extract all organization fields. Mark any field that cannot be confidently extracted as `[TO CONFIRM]`. Do not invent values.

Extraction map:

| Field | What to look for |
|---|---|
| `name` | Legal or trading name of the organization |
| `entity_type` | What kind of entity — startup, corporation, holding, cooperative, public company, NGO, government, other |
| `website` | Official URL |
| `founded` | Year founded |
| `headquarters` | City and country |
| `size` | Team size or employee range |
| `revenue_scale` | Annual revenue, turnover, or financial scale if stated |
| `industry` | Primary sector or domain |
| `description` | What the organization does and the value it delivers |
| `strategic_priorities` | Current strategic priorities or focus areas explicitly stated |
| `culture` | Decision-making style, risk tolerance, or openness to change if described |
| `regulatory_environment` | Regulatory bodies, compliance requirements, or legal constraints mentioned |
| `key_contacts` | Named contacts with roles |
| `challenge_domains` | Specific problem areas or pain points stated in the document |
| `prior_relationship` | Any existing relationship with the program or parent entity |

Default `has_units` to `false` unless the document explicitly describes sub-entities, divisions, or business units.

### Mode C — Web search

Ask the operator:

```
Provide the organization name to search:
```

Search verified public sources (official website, LinkedIn, Crunchbase, press releases, news) to extract organization fields. Mark any field not confirmed from public sources as `[TO CONFIRM]`. Do not invent values.

---

## Step 5 — Present preview for review

Display all collected data before creating any files:

```
Organization setup preview — [Organization Name]

  name:                   [value or TO CONFIRM]
  type:                   organization
  parent:                 [parent-slug]
  mode:                   [value or "inherited from [parent-slug]"]
  status:                 active
  has_units:              [true / false]

  entity_type:            [value or TO CONFIRM]
  website:                [value or TO CONFIRM]
  founded:                [value or TO CONFIRM]
  headquarters:           [value or TO CONFIRM]
  size:                   [value or TO CONFIRM]
  revenue_scale:          [value or TO CONFIRM]
  industry:               [value or TO CONFIRM]
  description:            [value or TO CONFIRM]
  strategic_priorities:   [value or TO CONFIRM]
  culture:                [value or TO CONFIRM]
  regulatory_environment: [value or TO CONFIRM]
  key_contacts:           [value or TO CONFIRM]
  challenge_domains:      [value or TO CONFIRM]
  prior_relationship:     [value or TO CONFIRM]

Does this look correct?
  - Type YES to proceed.
  - Type EDIT followed by corrections to adjust.
  - Type CANCEL to stop without creating any files.
```

Wait for the operator's response. If EDIT, apply corrections and redisplay. Wait for YES before proceeding.

---

## Step 6 — Derive organization slug

Normalize the organization name: lowercase, hyphen-separated, no special characters, no accents.

Example: "Acme Logistics Corp" → `acme-logistics-corp`

Determine folder path based on parent type:

- Parent is `program` → `programs/[program-slug]/orgs/[org-slug]/`
- Parent is `batch` → `programs/[program-slug]/batches/[batch-slug]/orgs/[org-slug]/`

If a folder already exists at that path, notify the operator:

```
An organization with slug "[org-slug]" already exists under "[parent-slug]".
Do you want to (A) overwrite the existing org-context.md or (B) cancel?
```

Wait for the operator's response.

---

## Step 7 — Create folder structure and generate org-context.md

Create the organization folder and all operational subdirectories:

If parent is `program`:
```
programs/[program-slug]/
└── orgs/
    └── [org-slug]/
        ├── org-context.md
        ├── challenges/
        ├── solvers/
        ├── pilots/
        ├── blockers/
        └── decisions/
```

If parent is `batch`:
```
programs/[program-slug]/
└── batches/
    └── [batch-slug]/
        └── orgs/
            └── [org-slug]/
                ├── org-context.md
                ├── challenges/
                ├── solvers/
                ├── pilots/
                ├── blockers/
                └── decisions/
```

**Note:** Operational subdirectories are always created at the organization level, even if `has_units: true`. Units will be created with their own subdirectories via `/vpm-setup-unit`. When a unit is added to an org, artefacts are migrated from the org level to the unit level and the org's operational subdirs are removed.

**If parent is a batch:** after creating the org folder, open `programs/[program-slug]/batches/[batch-slug]/batch-context.md` and add the new organization to the `## Organizations` section. If the section does not exist, append it at the end of the file:

```markdown
## Organizations

| Slug | Name | Status |
|---|---|---|
| [org-slug] | [Organization Name] | active |
```

Add one row per organization as they are onboarded. Do not remove existing rows.

Use this template for `org-context.md`:

```markdown
# [Organization Name] — Organization Context

## Identity

| Field | Value |
|---|---|
| Name | [Organization Name] |
| Slug | [org-slug] |
| Type | organization |
| Parent | [parent-slug] |
| Mode | [venture-client / venture-building / hybrid / inherited from [parent-slug]] |
| Status | active |
| Has Units | false |
| Created | [YYYY-MM-DD] |

---

## Description

[What the organization does and the value it delivers.]

---

## Profile

| Field | Value |
|---|---|
| Entity Type | [startup / corporation / holding / cooperative / public company / NGO / government / other] |
| Website | [URL or TO CONFIRM] |
| Founded | [Year or TO CONFIRM] |
| Headquarters | [City, Country or TO CONFIRM] |
| Size | [Range or TO CONFIRM] |
| Revenue Scale | [Annual revenue or financial scale, or TO CONFIRM] |
| Industry | [Sector or TO CONFIRM] |

---

## Strategic Priorities

[Current strategic priorities or focus areas, or TO CONFIRM]

---

## Culture

[Decision-making style, risk tolerance, and openness to change, or TO CONFIRM]

---

## Regulatory Environment

[Key regulations, compliance requirements, or oversight bodies that affect this organization, or none]

---

## Key Contacts

| Role | Name | Contact |
|---|---|---|
| Primary Contact | [Name] | [Contact] |

---

## Challenge Domains

[Specific problem areas or pain points this organization brings to the program, or TO CONFIRM]

---

## Prior Relationship

[Any existing relationship with the program or parent entity, or none]

---

## Active Challenges

_Populated by /vpm-challenge-workshop._

| Challenge | Title | Status |
|---|---|---|
| _None yet_ | — | — |

---

## Fields to Confirm

- [ ] [Field] — [why it matters]

---

## Notes

[Any additional context.]
```

---

## Step 8 — Confirm and suggest next step

```
Organization created — [Organization Name]

  Slug:      [org-slug]
  Parent:    [parent-slug]
  Mode:      [mode or inherited]
  Has units: false
  Status:    active

  Fields confirmed:   [N]
  Fields to confirm:  [N]

Files created:
  [org folder path]/org-context.md
  [org folder path]/challenges/
  [org folder path]/solvers/
  [org folder path]/pilots/
  [org folder path]/blockers/
  [org folder path]/decisions/

[If parent is a batch:]
Parent updated:
  programs/[program-slug]/batches/[batch-slug]/batch-context.md — [org-slug] added to Organizations

Next step:
  Run /vpm-setup-unit to add business units or sub-teams, or
  Run /vpm-challenge-workshop to define challenges directly for this organization.
```

---

## Quality criteria

- [ ] Parent entity was validated before data collection began.
- [ ] Input mode was selected before data collection began.
- [ ] All fields not provided or found were marked [TO CONFIRM] — not invented.
- [ ] Operator reviewed and approved the preview before any file was created.
- [ ] All six operational subdirectories were created.
- [ ] `has_units` defaults to `false` unless explicitly set otherwise.
- [ ] Slug: lowercase, hyphen-separated, no accents.
- [ ] Folder path matches parent type (orgs/ under program, or orgs/ under batch).
- [ ] If parent is a batch: batch-context.md was updated with the new org in ## Organizations.
- [ ] Confirmation summary shows confirmed and unconfirmed field counts.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-setup-program` or `/vpm-setup-batch` | Parent must exist before an org can be created. |
| Followed by | `/vpm-setup-unit` | Add sub-teams or business units to this organization. |
| Followed by | `/vpm-challenge-workshop` | Define and prioritize challenges for this organization. |
| Followed by | `/vpm-init` | Re-run to see the updated dashboard. |

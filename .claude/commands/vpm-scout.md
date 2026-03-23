# /vpm-scout

Sources solvers for a specific challenge. Supports sprint-based or single-round scouting with adjustable search criteria. Generates structured solver profiles and maintains a persistent scouting log in the challenge file.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To run scouting for a challenge, please provide:

1. Program slug
   Example: global-innovation-sprint

2. Batch slug (optional)
   Leave blank if the organization is directly under the program.
   Example: cohort-2026-q1

3. Organization slug
   Example: acme-logistics-corp

4. Unit slug (optional)
   Leave blank if challenges belong to the organization directly.
   Example: last-mile-operations

5. Challenge slug
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

Check that both files exist:
- `programs/[program-slug]/program-context.md`
- `[owner-folder]/challenges/[challenge-slug].md`

If the program is not found:
```
Program "[program-slug]" not found.
Run /vpm-setup-program to initialize it first.
```

If the challenge is not found:
```
Challenge "[challenge-slug]" not found in the specified owner folder.
Run /vpm-challenge-workshop to create challenges first.
```

Stop and do not proceed if either is missing.

---

## Step 3 — Read context

Read the following files and extract the fields listed:

**`programs/[program-slug]/program-context.md`**
- Mode, geography, domains, solver eligibility criteria

**`programs/[program-slug]/batches/[batch-slug]/batch-context.md`** (if batch exists)
- Any overrides to mode, geography, or eligibility

**`[owner-folder]/challenges/[challenge-slug].md`**
- Challenge title and problem statement
- Innovation horizon (Efficient / Sustaining / Transformative)
- Success criteria
- Known constraints
- Owner slug (org or unit)

Also check whether `## Scouting Criteria` and `## Scouting Log` already exist in the challenge file. If they do, read them — they indicate a scouting session was previously started for this challenge.

Use all of this context to derive default scouting criteria in Step 4.

---

## Step 4 — Derive and confirm scouting criteria

Derive default scouting criteria from the context read in Step 3. Present them to the operator for confirmation or adjustment:

```
Scouting Criteria — [Challenge Title]

Derived from program and challenge context:

  Geography:          [e.g., Latin America & Caribbean]
  Company stage:      [e.g., Startup / Scale-up]
  Funding stage:      [e.g., Up to Series B / Max $10M raised]
  Annual revenue:     [e.g., Max $5M]
  Tech maturity:      [e.g., Growth stage or Enterprise-ready]
  Language:           [e.g., Spanish / English]
  Sector focus:       [e.g., Agri-tech, food systems]
  Impact requirement: [e.g., Must demonstrate social or environmental impact]
  Exclusions:         [e.g., None / Pure consulting firms]

Would you like to adjust any of these? (yes / no)
If yes, specify which criteria to change and their new values.
```

Wait for the operator's response.

Apply any adjustments the operator specifies. Then save the criteria to the challenge file under `## Scouting Criteria` (create the section if it does not exist; update it if it does). Always save — even if no adjustments were made.

```markdown
## Scouting Criteria

Derived from program and challenge context. Last updated: [YYYY-MM-DD].

| Criterion | Value |
|---|---|
| Geography | [value] |
| Company stage | [value] |
| Funding stage | [value] |
| Annual revenue | [value] |
| Tech maturity | [value] |
| Language | [value] |
| Sector focus | [value] |
| Impact requirement | [value] |
| Exclusions | [value] |
```

---

## Step 5 — Define scouting approach

Ask the operator:

```
Scouting approach — [Challenge Title]

  A) Single round — generate all candidates at once
  B) Sprint — review candidates in batches

Type A or B.
If B, how many candidates per sprint? (default: 5)
```

Wait for the operator's response. Store the chosen mode and sprint size for use in later steps.

---

## Step 6 — Determine solver source

Ask the operator:

```
Solver source — [Challenge Title]

Challenge: [Problem statement summary — 1 sentence]
Horizon:   [Efficient / Sustaining / Transformative]

How would you like to proceed?

  A) I have specific solvers to evaluate — provide their names
  B) Suggest solver candidates based on the challenge profile and scouting criteria
  C) Both — I have some names and want additional suggestions

Type A, B, or C, and provide solver names if applicable.
```

Wait for the operator's response before proceeding.

---

## Step 7A — Named solvers (Mode A or C)

For each solver name provided, ask:

```
For each solver, share what you know (leave blank what you don't):

Solver: [Name]
  - Website or brief description (optional)
  - How was this solver found? (referral, inbound, event, database, other)
  - Any prior contact or relationship with this organization?
  - Anything else relevant?
```

Wait for the operator's response. Named solvers are always profiled directly — they bypass the sprint selection loop and go straight to Step 8.

Add each named solver to the `## Scouting Log` with source: `named` and profiled: `yes`.

If Mode C, proceed to Step 7B after profiling named solvers.

---

## Step 7B — Sprint or single-round suggestion loop (Mode B or C)

### Load scouting log

Before generating any candidates, read the `## Scouting Log` in the challenge file (if it exists). Extract all candidate names already listed. Do not suggest any candidate already present in the log.

### Single round (Mode A from Step 5)

Generate a list of solver candidates based on the challenge context and confirmed scouting criteria. Exclude candidates already in the scouting log.

```
Suggested solver candidates — [Challenge Title]

Criteria applied: [geography] · [sector] · [funding stage] · [impact requirement]

  1. [Solver name] — [1-sentence rationale]
  2. [Solver name] — [1-sentence rationale]
  3. [Solver name] — [1-sentence rationale]
  ...

Which would you like to profile?
  - Enter numbers (e.g., 1, 3) to select specific candidates
  - Type "all" to profile all
  - Type "none" to end scouting without profiling
```

Wait for the operator's selection. Add all presented candidates to the scouting log (profiled: yes for selected, profiled: no for skipped). Proceed to Step 8 with selected candidates.

---

### Sprint mode (Mode B from Step 5)

Run the following loop. Track the current sprint number starting at 1.

**Present sprint batch:**

Generate [sprint size] candidates not already in the scouting log, based on the confirmed scouting criteria.

```
Sprint [N] — [Challenge Title]
Criteria: [geography] · [sector] · [funding stage] · [impact requirement]

  1. [Solver name] — [1-sentence rationale]
  2. [Solver name] — [1-sentence rationale]
  3. [Solver name] — [1-sentence rationale]
  ...

Which would you like to profile?
  - Enter numbers (e.g., 1, 3) to select
  - Type "all" to profile all
  - Type "search" to get a new set of candidates for this sprint (same criteria, new names)
  - Type "none" to skip this sprint and end scouting
```

Wait for the operator's response.

**If operator types "search":**
Add all currently presented candidates to the scouting log (profiled: no). Generate a new set of [sprint size] candidates not in the scouting log. Display with the same sprint number and the label `(re-search)`. Repeat the selection prompt. An operator may re-search as many times as needed within the same sprint.

**If operator selects candidates (numbers or "all"):**
Proceed to Step 8 to profile selected candidates. Add all presented candidates to the scouting log (profiled: yes for selected, profiled: no for skipped).

After profiling is complete, ask whether the operator wants to refine the scouting criteria before the next sprint:

```
Sprint [N] complete — [N] solver(s) profiled.

Based on this sprint's selections, would you like to adjust the scouting criteria for the next sprint? (yes / no)
If yes, specify which criteria to change and their new values.
```

If yes: apply the adjustments, save the updated criteria to the challenge file under `## Scouting Criteria` with an updated timestamp, then continue.

Then ask:

```
  - Type "next" to run sprint [N+1]
  - Type "stop" to end scouting
```

If "next": increment sprint counter, return to the top of this loop.
If "stop": proceed to Step 9.

**If operator types "none":**
Add all presented candidates to the scouting log (profiled: no). End scouting and proceed to Step 9.

---

## Step 8 — Generate one solver profile per confirmed solver

For each solver selected for profiling, create `[owner-folder]/solvers/[solver-slug].md` using the template below.

Derive the solver slug: lowercase, hyphen-separated, no special characters, no accents.
Example: "Bringg Technologies" → `bringg-technologies`

If a solver file already exists at that path, notify the operator:
```
A profile for [solver-slug] already exists for this challenge owner.
Do you want to (A) update the existing profile or (B) skip this solver?
```

**Eligibility pre-check:** Before generating a full profile, compare what you know about the solver against the solver eligibility criteria defined in the program and batch context files (read in Step 3). These criteria vary by program — do not assume specific rules. If a likely disqualifier is detected, alert the operator before writing the file:

```
[Solver Name] may not meet the eligibility criteria for this program/batch:
  — [specific criterion] appears to conflict with [what you know about the solver]

Profile anyway as a strategic reference? (yes / no / skip)
```

If the operator chooses to profile anyway, note the eligibility flag in the Interaction Log and set `Data confidence` accordingly.

Pre-fill every field you can infer from your knowledge of the company. Mark any field you cannot confidently fill as `_To be validated._`. Do not invent specific client names or financial figures.

```markdown
---
slug: [solver-slug]
type: solver
status: in-evaluation
challenge: [challenge-slug]
owner: [org-slug or unit-slug]
program: [program-slug]
added: [YYYY-MM-DD]
---

# [Solver Name] — Solver Profile

## Company Overview

| Field | Value |
|---|---|
| Name | [Solver Name] |
| Slug | [solver-slug] |
| Program | [program-slug] |
| Owner | [org-slug or unit-slug] |
| Website | [URL if known] |
| Founded | [Year if known] |
| Headquarters | [City, Country if known] |
| Team size | [Range if known — e.g., 50–200] |
| Funding stage | [Bootstrapped / Seed / Series A / Series B+ / Public / Unknown] |
| Total funding | [Amount if known, otherwise _Unknown_] |
| Source | [How this solver was found: referral / inbound / suggestion / database / event] |
| Added | [Today's date — YYYY-MM-DD] |
| Status | in-evaluation |
| Data confidence | [High — verified public data / Medium — some fields estimated / Low — identity or core details unconfirmed] |

---

## Value Proposition

[2–3 sentences describing what this solver does and the core value it delivers. Focus on the outcome for the buyer, not the technology itself.]

---

## Technology & Product

| Dimension | Description |
|---|---|
| Core technology | [Main technical approach or stack — e.g., computer vision, NLP, IoT sensors, API platform] |
| Product type | [SaaS / Platform / Hardware + Software / Professional services / Marketplace] |
| Deployment model | [Cloud / On-premise / Hybrid / Edge] |
| Integration approach | [API-first / Pre-built connectors / Custom integration / No integration required] |
| Maturity level | [MVP / Early product / Growth stage / Enterprise-ready] |
| Time to deploy | [Estimated range — e.g., 2–6 weeks, 3–6 months] |

---

## Business Model

| Dimension | Description |
|---|---|
| Revenue model | [Subscription / Usage-based / License / Transaction fee / Services / Hybrid] |
| Typical contract | [Monthly / Annual / Multi-year / Project-based] |
| Pricing signal | [Indicative range or model if known, otherwise _Unknown_] |
| Minimum engagement | [Minimum contract size or scope if known] |

---

## Reference Clients

Known clients or deployment contexts relevant to this challenge. Do not invent names.

| Client / Sector | Use case | Relevance to this challenge |
|---|---|---|
| [Client name or sector] | [What they use it for] | [Why this is a relevant reference] |
| _Additional references to be validated_ | | |

---

## Fit Assessment

### Strategic Fit
Score: [1–5] — [Low / Medium / High]

[2–3 sentences assessing how well this solver's direction, vision, and positioning align with the challenge and the program's goals. Consider: Is this a core use case for them or a stretch? Are they building toward or away from this problem?]

### Technical Fit
Score: [1–5] — [Low / Medium / High]

[2–3 sentences assessing whether the solver's technology can realistically address the challenge. Consider: Does their tech stack match the integration constraints? Is their maturity level appropriate for a pilot? Are there known technical risks or gaps?]

### Operational & Cultural Fit
Score: [1–5] — [Low / Medium / High]

[2–3 sentences assessing whether the solver can work effectively with the target organization. Consider: Company size mismatch, geographic coverage, language, support model, sales motion (PLG vs enterprise), and willingness to engage in a structured pilot.]

### Overall Fit Score
`(Strategic + Technical + Operational) / 3 = [X.X] / 5`

_Scores are preliminary — generated from public information without direct contact. Final fit determination is made in /vpm-fit-check._

---

## Integration Feasibility

| Dimension | Assessment | Notes |
|---|---|---|
| Data requirements | [Low / Medium / High complexity] | _To be validated_ |
| IT / security requirements | [Low / Medium / High] | _To be validated_ |
| Change management | [Low / Medium / High] | _To be validated_ |
| Estimated pilot duration | [Range] | _To be validated_ |
| Estimated pilot cost | _To be validated_ | Confirm with solver |

---

## Risks

| Risk | Type | Likelihood | Mitigation |
|---|---|---|---|
| [Describe risk] | [technical / operational / budget / cultural / regulatory / political] | [High / Medium / Low] | _To be defined_ |
| [Describe risk] | [Type] | [Likelihood] | _To be defined_ |

---

## Open Questions

Questions to answer before advancing this solver to fit check or pilot.

- [Question about technology, integration, pricing, or deployment]
- [Question about reference clients or relevant prior experience]
- [Question about team, support model, or geographic coverage]
- [Question about willingness to engage in a structured pilot with defined success criteria]

---

## Linked Challenges

| Challenge | Fit score | Status |
|---|---|---|
| [challenge-slug] | [Overall fit score] | in-evaluation |

---

## Interaction Log

| Date | Type | Participants | Summary |
|---|---|---|---|
| [Today's date] | Profile created | — | Scouted for [challenge-slug] |
```

---

## Step 9 — Update challenge file

Open `[owner-folder]/challenges/[challenge-slug].md` and make the following updates:

**1. Linked Solvers** — add one row per new solver profiled. If the section already has rows, append without removing existing entries:

```markdown
## Linked Solvers

| Solver | Fit score | Status |
|---|---|---|
| [solver-slug] | [Overall fit score] | in-evaluation |
```

**2. Challenge status** — update both the frontmatter and the `## Challenge Overview` table:

In the frontmatter block at the top of the file:
```yaml
status: in-evaluation
```

In the `## Challenge Overview` table:
```markdown
| Status | in-evaluation |
```

**3. Scouting Criteria** — if criteria were adjusted at any point during the session (Step 4 or sprint refinement checkpoints), ensure the `## Scouting Criteria` section in the challenge file reflects the latest values with an updated timestamp. If no adjustments were made, no action needed.

**4. Scouting Log** — update or create the `## Scouting Log` section. Append all candidates presented during this session. Do not remove existing rows — always append.

```markdown
## Scouting Log

| Sprint | Candidate | Source | Profiled | Date |
|---|---|---|---|---|
| [N] | [Solver Name] | [suggestion / named] | [yes / no] | [YYYY-MM-DD] |
| [N] (re-search) | [Solver Name] | suggestion | [yes / no] | [YYYY-MM-DD] |
```

**5. Owner context sync** — open `[owner-folder]/org-context.md` (or `unit-context.md`) and update the `Status` column for this challenge in `## Active Challenges` from `active` to `in-evaluation`.

---

## Step 10 — Confirm and suggest next step

Read the full `## Linked Solvers` section from the challenge file to build the complete pipeline view — including solvers profiled in previous sessions, not just this one.

```
Scouting completed — [Challenge Title]

  Program:    [program-slug]
  Owner:      [org-slug or unit-slug]
  Challenge:  [challenge-slug]
  Sprints run (this session):      [N]
  Candidates seen (this session):  [N total across all sprints and re-searches]
  Solvers profiled (this session): [N]
  Solvers profiled (total):        [N — from challenge file Linked Solvers section]

Files created this session:
  [owner-folder]/solvers/[solver-slug-1].md  (fit: [X.X]/5)
  [owner-folder]/solvers/[solver-slug-2].md  (fit: [X.X]/5)
  ...

Challenge file updated:
  [owner-folder]/challenges/[challenge-slug].md
    — [N] solver(s) linked; status → in-evaluation
    — Scouting Criteria saved
    — Scouting Log updated ([N] candidates recorded)
Owner context updated:
  [owner-folder]/org-context.md — challenge status → in-evaluation

Full solver pipeline — [challenge-slug]  ([N total] solvers):

  [solver-name]   [X.X] / 5   [flag if any — e.g., ⚠ eligibility / ⚠ validate identity]
  [solver-name]   [X.X] / 5
  ...
  (sorted by fit score, highest first)

Next step: run /vpm-fit-check to run a structured fit evaluation and produce a shortlist recommendation.
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-challenge-workshop` | Formalized challenges with problem statements and constraints must exist before scouting begins. |
| Followed by | `/vpm-fit-check` | Solver profiles generated here are the input for structured fit evaluation and shortlisting. |

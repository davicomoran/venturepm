# /vpm-scout

Sources and evaluates solvers for a specific challenge within a program. Generates a structured solver profile with value proposition, tech stack, business model, current clients, and fit assessment across three dimensions: strategic, technical, and operational-cultural.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To run scouting for a challenge, please provide:

1. Program slug
   The slug of the active program.
   Example: global-innovation-sprint

2. Challenge slug
   The slug of the challenge to scout solvers for.
   Example: acme-logistics-corp-last-mile-visibility
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate program and challenge exist

Check that both files exist:
- `programs/[program-slug]/program-context.md`
- `programs/[program-slug]/challenges/[challenge-slug].md`

If the program is not found:
```
Program "[program-slug]" not found.
Run /vpm-setup-program to initialize it first.
```

If the challenge is not found:
```
Challenge "[challenge-slug]" not found in program "[program-slug]".
Run /vpm-challenge-workshop to create challenges first.
```

Stop and do not proceed if either is missing.

---

## Step 3 — Read challenge context

Read `programs/[program-slug]/challenges/[challenge-slug].md` and extract:
- Challenge title and problem statement
- Innovation horizon (Efficient / Sustaining / Transformative)
- Success criteria
- Known constraints
- Organization slug (to understand the deployment context)

Use this context to inform fit assessment in later steps.

---

## Step 4 — Determine scouting mode

Ask the operator:

```
Scouting — [Challenge Title]

Challenge: [Problem statement summary — 1 sentence]
Horizon:   [Efficient / Sustaining / Transformative]

How would you like to proceed?

  A) I have specific solvers to evaluate — provide their names
  B) Suggest solver candidates based on the challenge profile
  C) Both — I have some names and want additional suggestions

Type A, B, or C, and provide solver names if applicable.
```

Wait for the operator's response before proceeding.

---

## Step 5A — If operator provides solver names (A or C)

For each solver name provided, ask:

```
For each solver, share what you know (leave blank what you don't):

Solver: [Name]
  - Website or brief description (optional)
  - How was this solver found? (referral, inbound, event, database, other)
  - Any prior contact or relationship with this organization?
  - Anything else relevant?
```

Wait for the operator's response.

---

## Step 5B — If operator requests suggestions (B or C)

Based on the challenge context and your knowledge, generate a list of solver archetypes or known companies that could address this challenge. Present them as candidates for the operator to confirm:

```
Suggested solver candidates for [Challenge Title]:

Based on the challenge profile, these types of solutions are most likely to be relevant:

  1. [Solver name or archetype] — [1-sentence rationale]
  2. [Solver name or archetype] — [1-sentence rationale]
  3. [Solver name or archetype] — [1-sentence rationale]
  4. [Solver name or archetype] — [1-sentence rationale]
  5. [Solver name or archetype] — [1-sentence rationale]

Which of these should be profiled? You can confirm all, select a subset, or add names not listed.
```

Wait for the operator's confirmation.

---

## Step 6 — Derive solver slugs

For each confirmed solver, derive a slug: lowercase, hyphen-separated, no special characters, no accents.

Example: "Bringg Technologies" → `bringg-technologies`

File path: `programs/[program-slug]/solvers/[solver-slug].md`

If a solver file already exists at that path, notify the operator:
```
A profile for [solver-slug] already exists in this program.
Do you want to (A) update the existing profile or (B) skip this solver?
```

---

## Step 7 — Generate one solver profile per confirmed solver

For each solver, create `programs/[program-slug]/solvers/[solver-slug].md` using the template below.

Pre-fill every field you can infer from your knowledge of the company. Mark any field you cannot confidently fill as `_To be validated._`. Do not invent specific client names or financial figures.

```markdown
# [Solver Name] — Solver Profile

## Company Overview

| Field | Value |
|---|---|
| Name | [Solver Name] |
| Slug | [solver-slug] |
| Program | [program-slug] |
| Website | [URL if known] |
| Founded | [Year if known] |
| Headquarters | [City, Country if known] |
| Team size | [Range if known — e.g., 50–200] |
| Funding stage | [Bootstrapped / Seed / Series A / Series B+ / Public / Unknown] |
| Total funding | [Amount if known, otherwise _Unknown_] |
| Source | [How this solver was found: referral / inbound / suggestion / database / event] |
| Added | [Today's date — YYYY-MM-DD] |
| Status | in-evaluation |

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

## Step 8 — Update challenge file with solver count

Open `programs/[program-slug]/challenges/[challenge-slug].md` and update the **Linked Solvers** section:

```markdown
## Linked Solvers

| Solver | Fit score | Status |
|---|---|---|
| [solver-slug] | [Overall fit score] | in-evaluation |
```

Add one row per new solver. If the section already has rows, append without removing existing entries.

---

## Step 9 — Confirm and suggest next step

```
Scouting completed — [Challenge Title]

  Program:    [program-slug]
  Challenge:  [challenge-slug]
  Solvers profiled: [N]

Files created:
  programs/[program-slug]/solvers/[solver-slug-1].md  (fit: [X.X]/5)
  programs/[program-slug]/solvers/[solver-slug-2].md  (fit: [X.X]/5)
  ...

Challenge file updated:
  programs/[program-slug]/challenges/[challenge-slug].md — [N] solver(s) linked.

Next step: run /vpm-fit-check to run a structured fit evaluation and produce a shortlist recommendation.
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-challenge-workshop` | Formalized challenges with problem statements and constraints must exist before scouting begins. |
| Followed by | `/vpm-fit-check` | Solver profiles generated here are the input for structured fit evaluation and shortlisting. |

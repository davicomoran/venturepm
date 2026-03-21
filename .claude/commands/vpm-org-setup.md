# /vpm-org-setup

Onboards a new organization into an active program. Generates a full organization profile including industry context, stakeholder map, power map, challenge signals, innovation risks, and open questions to validate in first interviews.

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

```
To onboard a new organization, please provide:

1. Program slug
   The slug of the active program this organization is joining.
   Example: global-innovation-sprint

2. Organization name
   Full name of the organization being onboarded.
   Example: Acme Logistics Corp
```

Wait for the operator's response before proceeding.

---

## Step 2 — Validate program exists

Check that `programs/[program-slug]/program-context.md` exists.

If it does not exist, respond:

```
Program "[program-slug]" not found.

Run /vpm-setup-program to initialize it before onboarding an organization.
```

Stop and do not create any files.

---

## Step 3 — Derive the organization slug

Convert the organization name to a slug: lowercase, hyphen-separated, no special characters, no accents.

Example: "Acme Logistics Corp" → `acme-logistics-corp`

The output file path is: `programs/[program-slug]/solvers/[org-slug].md`

---

## Step 4 — Generate the organization profile

Create `programs/[program-slug]/solvers/[org-slug].md` using the template below.

Use your knowledge to pre-fill sections where reasonable inferences can be made from the organization name and context. Mark any field you cannot infer with `_To be validated._`

Do not invent specific names of individuals. Leave stakeholder names blank for the operator to fill in.

```markdown
# [Organization Name] — Organization Profile

## Overview

| Field | Value |
|---|---|
| Name | [Organization Name] |
| Slug | [org-slug] |
| Program | [program-slug] |
| Industry | [Primary industry — e.g., Logistics, Financial Services, Healthcare] |
| Sub-sector | [More specific segment if applicable] |
| Geography | [Primary country or region of operations] |
| Organization size | [Approximate employee count or revenue range if known] |
| Enrollment date | [Today's date — YYYY-MM-DD] |
| Status | active |

---

## Industry Context

[2–4 sentences describing the structural dynamics of the organization's industry that are relevant to innovation:
- Key competitive pressures or disruption vectors
- Regulatory or compliance constraints typical to the sector
- Technology adoption patterns (early adopter, late majority, etc.)
- Any macro trends (digitalization, sustainability mandates, consolidation) relevant to the program's scope]

---

## Stakeholder Map

Key roles typically involved in innovation initiatives at this type of organization. Names to be confirmed in first contact.

| Role | Typical Title | Involvement | Name |
|---|---|---|---|
| Executive sponsor | Chief [X] Officer / VP [X] | Approves pilots and budget | _To be confirmed_ |
| Innovation champion | Innovation Manager / Director | Day-to-day counterpart | _To be confirmed_ |
| Business unit owner | Director / Head of [BU] | Owns the challenge operationally | _To be confirmed_ |
| Procurement / legal | Legal Counsel / Procurement Lead | Reviews contracts and compliance | _To be confirmed_ |
| Technical gatekeeper | CTO / IT Director | Approves technical integration | _To be confirmed_ |

_Update this table after the first interview._

---

## Power Map

Assessment of internal dynamics that affect how decisions are made and how innovation moves (or stalls) inside this organization.

| Dimension | Assessment | Notes |
|---|---|---|
| Decision-making style | [Centralized / Distributed / Committee-driven] | _To be validated._ |
| Innovation appetite | [High / Medium / Low] | _To be validated._ |
| Budget autonomy | [BU-level / Corporate-level / Mixed] | _To be validated._ |
| Procurement complexity | [High / Medium / Low] | _To be validated._ |
| Pilot approval speed | [Fast (<30d) / Medium (30–90d) / Slow (>90d)] | _To be validated._ |
| Key influencer | _To be confirmed_ | Person whose buy-in is critical beyond formal hierarchy |

_Inferences above are based on industry norms. Validate in first interviews._

---

## Challenge Signals

Potential problem areas worth exploring as challenge candidates, based on industry context and organization profile. These are hypotheses — not confirmed challenges.

| Signal | Category | Confidence | Source |
|---|---|---|---|
| [Describe potential pain point or inefficiency] | [Operations / Commercial / Risk / Sustainability / Other] | [High / Medium / Low] | [Industry research / Inference / To be validated] |
| [Describe potential pain point or inefficiency] | [Category] | [Confidence] | [Source] |
| [Describe potential pain point or inefficiency] | [Category] | [Confidence] | [Source] |

_Run /vpm-challenge-workshop to formalize these signals into validated challenges._

---

## Innovation Risks

Factors that could slow down or block the innovation process for this organization.

| Risk | Type | Likelihood | Mitigation |
|---|---|---|---|
| [Describe risk] | [political / budget / technical / cultural / regulatory / operational] | [High / Medium / Low] | _To be defined._ |
| [Describe risk] | [Type] | [Likelihood] | _To be defined._ |
| [Describe risk] | [Type] | [Likelihood] | _To be defined._ |

_Update after first interviews and revisit throughout the program._

---

## Open Questions

Questions to validate or answer in first interviews with this organization.

**Strategic alignment**
- What are the top 3 priorities for this organization in the next 12 months?
- Where does innovation sit in the organizational agenda — strategic priority or exploratory initiative?
- Has this organization run a pilot with an external startup before? What was the outcome?

**Operational context**
- Which business units or functions are most open to piloting new solutions?
- What does a typical procurement or vendor approval process look like?
- Are there budget cycles or fiscal constraints that affect pilot timing?

**Challenge validation**
- Which of the challenge signals identified above resonates most?
- Are there problems the organization has tried to solve internally without success?
- What would a successful pilot look like from their perspective?

**Stakeholder access**
- Who else should be involved in the challenge definition process?
- Who has the authority to approve a pilot budget?

---

## Interaction Log

_Record key touchpoints, decisions, and context from interactions with this organization._

| Date | Type | Participants | Summary |
|---|---|---|---|
| [YYYY-MM-DD] | [Email / Call / Workshop / Meeting] | [Names or roles] | [Brief summary] |

---

## Linked Challenges

_Populated by /vpm-challenge-workshop._

| Challenge | Status | Owner |
|---|---|---|
| _None yet_ | — | — |

---

## Linked Pilots

_Populated when a pilot is initiated._

| Pilot | Status | Start date |
|---|---|---|
| _None yet_ | — | — |
```

---

## Step 5 — Update program-context.md

Open `programs/[program-slug]/program-context.md` and add the organization to the **Organizations Enrolled** section:

```markdown
| [Organization Name] | [org-slug] | active | [Today's date — YYYY-MM-DD] |
```

If the table does not yet exist in that section, replace the placeholder text with:

```markdown
## Organizations Enrolled

| Organization | Slug | Status | Enrolled |
|---|---|---|---|
| [Organization Name] | [org-slug] | active | [Today's date — YYYY-MM-DD] |
```

---

## Step 6 — Confirm and suggest next step

```
Organization onboarded successfully.

  Organization:  [Organization Name]
  Slug:          [org-slug]
  Program:       [program-slug]

Files created or updated:
  programs/[program-slug]/solvers/[org-slug].md
  programs/[program-slug]/program-context.md  (Organizations Enrolled updated)

Next step: run /vpm-challenge-workshop to turn challenge signals into validated challenges for this organization.
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-setup-program` | The program must exist before an organization can be enrolled. |
| Followed by | `/vpm-challenge-workshop` | Challenge signals identified here are formalized in the workshop. |

# VenturePM — CLAUDE.md

**Author:** David Moran — [github.com/davicomoran](https://github.com/davicomoran)
**Repository:** [github.com/davicomoran/venturepm](https://github.com/davicomoran/venturepm)
**License:** Open Source

---

## What is VenturePM?

VenturePM is an open-source operating system for managing corporate innovation programs. It provides a structured methodology, vocabulary, and toolset for running **Venture Client** and **Venture Building** programs — or any hybrid combination of both.

It is **program-agnostic**: it works for corporate innovation units, accelerators, venture studios, and any organization that systematically works with startups or builds new ventures internally. No vendor lock-in, no proprietary assumptions.

The OS is operated through Claude as an AI co-pilot that manages pipeline state, surfaces blockers, tracks decisions, and guides program managers through each phase.

---

## Operating Modes

VenturePM supports three modes. Each program declares its mode at initialization.

| Mode | Description |
|---|---|
| `venture-client` | The organization buys or pilots solutions from external startups. Focus: scouting, evaluation, and pilots. |
| `venture-building` | The organization builds new ventures internally or with founders-in-residence. Focus: problem validation, team formation, and spin-off. |
| `hybrid` | Combines both: scouts external solutions while incubating internal ventures in parallel. |

The mode determines which phases, templates, and metrics are active for a given program.

---

## Organizational Hierarchy

VenturePM uses a four-level hierarchy. `program` and `organization` are required. `batch` and `unit` are optional.

```
program
└── batch (optional)
    └── organization
        └── unit (optional)
```

| Level | Required | Definition |
|---|---|---|
| `program` | Yes | The top-level innovation initiative. Has no parent. Context container only — no operational subdirectories. |
| `batch` | No | A specific cohort or instance of a program with its own context, rules, and conditions. Context container only — no operational subdirectories. |
| `organization` | Yes | The challenger entity participating in the program. Defines challenges and evaluates solvers. If it has units, challenges are owned by the units. If it has no units, it is the lowest active level and owns challenges directly. |
| `unit` | No | A sub-entity within an organization — a division, team, or business unit. When units exist, they are always the lowest active level and own all challenges, solvers, pilots, blockers, and decisions. |

**These levels are functional positions, not fixed corporate titles.** Each level maps to whatever entity plays that role in a given program:

- `organization` can be a holding company, a corporation, a cooperative, an NGO, a startup, a government agency, or any entity that acts as the challenger.
- `unit` can be a business unit, a division, a department, a subsidiary, or any sub-entity within the organization where challenge ownership lives.

What matters is which entity owns challenges at the bottom of the hierarchy — that determines where operational artefacts are created. The same program structure accommodates very different real-world configurations depending on how the challenger entity is structured.

Challenges — and all operational artefacts — are always assigned to the lowest active level in the hierarchy.

---

## Standard Vocabulary

All entities, statuses, and commands use English regardless of the operator's language. This ensures consistency across documentation, exports, and integrations.

| Term | Definition |
|---|---|
| `program` | The top-level managed innovation initiative operating within VenturePM. |
| `batch` | A specific cohort or instance of a program with its own context and rules. Inherits from its parent program. |
| `organization` | A corporate entity or company participating in the program. Defines challenges and evaluates solvers. Always a challenger — not a solver. |
| `unit` | A sub-team or business unit within an organization. When units exist, they own all operational artefacts. |
| `solver` | A company (typically a startup) that proposes a solution to a challenge. Solvers are evaluation artefacts — they are not part of the organizational hierarchy. A solver profile is always created in the context of a specific challenge via `/vpm-scout`. |
| `challenge` | A specific problem statement owned by an organization or unit, published to attract solvers. |
| `pilot` | A time-boxed, scoped experiment that validates a solver's solution in a real context. |
| `fit` | The degree to which a solver's solution addresses a challenge (assessed during evaluation). |

---

## Base Process: 5-Phase Pipeline

Every program, regardless of mode, follows a five-phase pipeline. Phases may run sequentially or in parallel across multiple challenges.

```
Setup → Challenges → Scouting → Evaluation → Pilots
```

### Phase 1 — Setup
Define program parameters: mode, team, governance, success metrics, and organizational scope.

### Phase 2 — Challenges
Identify, validate, and publish problem statements. Each challenge is scoped to an organization or unit and has a defined owner.

### Phase 3 — Scouting
Source solvers through structured methods: inbound applications, proactive outreach, partner referrals, or internal ideation (for venture-building mode).

### Phase 4 — Evaluation
Assess solver-challenge fit using a defined scoring framework. Output: a ranked list of candidates with a recommendation (advance, hold, reject).

### Phase 5 — Pilots
Design, launch, and track time-boxed pilots with selected solvers. Measure outcomes against predefined success criteria. Output: pilot decision (scale, extend, stop).

---

## Operating Principles

These principles are grounded in publicly available Venture Client methodology and apply across all modes.

1. **Problem first.** Challenges are defined by real operational needs, not technology trends.
2. **Speed over perfection.** A 60-day pilot beats a 12-month evaluation. Bias toward action.
3. **Real budget, real stakes.** Pilots are funded commitments, not unpaid proof-of-concepts.
4. **Senior sponsorship.** Every challenge has an executive owner accountable for the pilot decision.
5. **Structured funnel.** Scouting volume → evaluation rigor → pilot selectivity. Each phase has explicit exit criteria.
6. **Separation of scouting and evaluation.** The team that scouts does not make the final evaluation call alone.
7. **Documented decisions.** Every pass/fail decision is recorded with a reason, not just an outcome.
8. **Continuous learning.** Blockers, learnings, and decisions feed back into the program to improve future cycles.

---

## Blocker Taxonomy

When a challenge, solver, or pilot is blocked, the blocker is classified using this generic taxonomy. Classification drives escalation paths and resolution strategies.

| Type | Description |
|---|---|
| `political` | Internal stakeholders are misaligned or withholding support. |
| `budget` | Funding is unavailable, frozen, or pending approval. |
| `technical` | Integration, infrastructure, or technical feasibility issues. |
| `cultural` | Resistance to change, risk aversion, or adoption challenges within the organization. |
| `regulatory` | Legal, compliance, data privacy, or procurement constraints. |
| `operational` | Process gaps, resource constraints, or capacity issues blocking execution. |

Each blocker has an owner, a classification, and a resolution status (`open`, `in-progress`, `resolved`, `escalated`).

---

## Prioritization Criteria

Challenges and solvers are prioritized using a two-axis framework:

```
Priority = Feasibility × Impact
```

| Axis | Definition |
|---|---|
| `feasibility` | How executable is this, given current constraints? (team, budget, time, technical readiness) |
| `impact` | How significant is the outcome if this succeeds? (business value, strategic alignment, scale potential) |

Both axes are scored 1–5. The product determines pipeline priority. Ties are broken by sponsorship strength and time sensitivity.

---

## File Map

The folder structure mirrors the organizational hierarchy. Entities are nested inside their parent — programs contain batches, batches contain orgs, orgs contain units.

**Context file naming convention:**
- `program` → `program-context.md`
- `batch` → `batch-context.md`
- `organization` → `org-context.md`
- `unit` → `unit-context.md`

**Operational subdirectories** (`challenges/`, `solvers/`, `pilots/`, `blockers/`, `decisions/`) are created only at the lowest active level:
- `organization` with no units → gets operational subdirectories
- `unit` → always gets operational subdirectories
- `program` and `batch` → never get operational subdirectories
- `organization` with units → no operational subdirectories (challenges belong to units)

---

**Full structure — program → batch → org → unit:**

```
venturepm/
├── CLAUDE.md
├── README.md
├── programs/
│   └── [program-slug]/
│       ├── program-context.md
│       ├── reports/                             # optional — created by /vpm-report on save
│       │   └── [report-slug].md
│       └── batches/
│           └── [batch-slug]/
│               ├── batch-context.md
│               └── orgs/
│                   └── [org-slug]/
│                       ├── org-context.md      # has_units: true → no operational subdirs
│                       └── units/
│                           └── [unit-slug]/
│                               ├── unit-context.md
│                               ├── challenges/
│                               │   └── [challenge-slug].md
│                               ├── solvers/
│                               │   └── [solver-slug].md
│                               ├── pilots/
│                               │   └── [pilot-slug].md
│                               ├── blockers/
│                               │   └── [challenge-slug]-blockers.md
│                               └── decisions/
│                                   └── [challenge-slug]-decisions.md
└── docs/
```

**Flat structure — program → org (no batch, no unit):**

```
programs/
└── [program-slug]/
    ├── program-context.md
    ├── reports/                         # optional — created by /vpm-report on save
    │   └── [report-slug].md
    └── orgs/
        └── [org-slug]/
            ├── org-context.md          # has_units: false → gets operational subdirs
            ├── challenges/
            ├── solvers/
            ├── pilots/
            ├── blockers/
            │   └── [challenge-slug]-blockers.md
            └── decisions/
                └── [challenge-slug]-decisions.md
```

---

**Context file fields by type:**

| Field | program | batch | organization | unit |
|---|---|---|---|---|
| `name` | ✓ | ✓ | ✓ | ✓ |
| `type` | ✓ | ✓ | ✓ | ✓ |
| `parent` | null | program slug | batch or program slug | org slug (relative) |
| `mode` | ✓ | inherited / override | inherited | inherited |
| `status` | ✓ | ✓ | ✓ | ✓ |
| `has_units` | — | — | ✓ | — |

---

## Entity Types & Linking

### How entities are linked

Every entity is a folder nested inside its parent. The `parent` field in the context file explicitly declares the relationship, enabling traversal for reporting and context inheritance.

```
batch-context.md
  name:   Cohort 2026-Q1
  type:   batch
  parent: [program-slug]   ← slug of parent program
  mode:   venture-client
```

A `program` entity has `parent: null`. All other types must declare a parent slug.

Solvers are not hierarchy members. A solver profile is stored inside the `solvers/` folder of the organization or unit that is evaluating it — always in the context of a specific challenge.

---

### Inheritance rule

A `batch` inherits the mode, governance, and success metrics of its parent `program` by default. It may override any of these by declaring its own values in `batch-context.md`. Overrides apply only within that batch and do not propagate upward.

An `organization` inherits context from whichever entity it is directly parented to — a `batch` if one exists, or a `program` if the batch level is skipped.

A `unit` always inherits from its parent `organization`.

---

### Challenge assignment rule

Challenges — and all operational artefacts — are always assigned to the **lowest active level** in the hierarchy:

- If a `unit` exists → challenges are assigned to the unit.
- If no `unit` exists → challenges are assigned to the `organization`.

Programs and batches are context containers. They never own challenges directly.

---

### Hierarchy examples

**Example A — Direct org with a sub-team owning challenges (program → org → unit)**

An organization participates in the program and delegates challenge ownership to one of its internal teams. The team is the lowest active level and owns all artefacts.

```
programs/
└── [program-slug]/
    ├── program-context.md
    └── orgs/
        └── [org-slug]/               (organization, has_units: true)
            ├── org-context.md
            └── units/
                └── [unit-slug]/      (unit — owns challenges)
                    ├── unit-context.md
                    ├── challenges/
                    ├── solvers/
                    ├── pilots/
                    ├── blockers/
                    └── decisions/
```

If the unit did not exist, the organization would have `has_units: false` and own the operational subdirectories directly.

---

**Example B — Multi-batch program (program → batch → org)**

A program runs multiple cohorts. Each cohort has its own participating organizations. Organizations have no units — they own challenges directly.

```
programs/
└── [program-slug]/
    ├── program-context.md
    └── batches/
        ├── [batch-slug-1]/           (batch)
        │   ├── batch-context.md
        │   └── orgs/
        │       └── [org-slug-a]/     (organization, has_units: false)
        │           ├── org-context.md
        │           ├── challenges/
        │           ├── solvers/
        │           └── pilots/
        └── [batch-slug-2]/           (batch)
            ├── batch-context.md
            └── orgs/
                └── [org-slug-b]/     (organization, has_units: false)
                    ├── org-context.md
                    ├── challenges/
                    ├── solvers/
                    └── pilots/
```

Each org owns its challenges. The program level is used for cross-batch reporting via `/vpm-report`.

---

**Example C — Conglomerate or holding as organization, divisions as units (program → org → unit)**

A large entity with multiple divisions participates as a single organization. Each division is a unit and owns its own challenges independently. The organization-level context captures the holding's profile; each unit's context captures the division's specific situation.

```
programs/
└── [program-slug]/
    ├── program-context.md
    └── orgs/
        └── [holding-slug]/           (organization, has_units: true)
            ├── org-context.md
            ├── units/
            │   ├── [division-a-slug]/  (unit — owns challenges)
            │   │   ├── unit-context.md
            │   │   ├── challenges/
            │   │   ├── solvers/
            │   │   ├── pilots/
            │   │   ├── blockers/
            │   │   └── decisions/
            │   └── [division-b-slug]/  (unit — owns challenges)
            │       ├── unit-context.md
            │       ├── challenges/
            │       ├── solvers/
            │       ├── pilots/
            │       ├── blockers/
            │       └── decisions/
```

If a division itself has internal sub-teams with distinct challenge ownership, treat the division as the organization and its sub-teams as units — placing the division one level up in the hierarchy.

---

## Command Reference

All VenturePM commands use the `vpm-` prefix.

| Command | Description |
|---|---|
| `/vpm-init` | Initialize session: show dashboard with active programs and pipeline status. |
| `/vpm-setup-program` | Set up a new program. Supports three input modes: manual entry, document import, or web search from verified sources. Creates `program-context.md`. |
| `/vpm-setup-batch` | Set up a new batch within a program. Supports manual entry, document import, or web search. Creates `batch-context.md`. |
| `/vpm-setup-org` | Set up a new organization within a program or batch. Supports manual entry, document import, or web search. Asks whether the org has units (`has_units: true/false`) and creates the appropriate folder structure. If `has_units: true`, instructs the operator to run `/vpm-setup-unit` for each unit. Creates `org-context.md`. |
| `/vpm-setup-unit` | Set up a new unit within an existing organization. Supports manual entry, document import, or web search. Creates the unit folder and all operational subdirectories under the parent org. Creates `unit-context.md`. Can be run at any time — units can be added to an org after initial setup. |
| `/vpm-new-command` | Create a new VenturePM command: generate the command file with standard structure and register it in CLAUDE.md. |
| `/vpm-challenge-workshop` | Facilitate challenge identification, classification by innovation horizon, and prioritization by feasibility × impact for an organization or unit. |
| `/vpm-scout` | Source solvers for a specific challenge. Solvers are evaluation artefacts — created exclusively through scouting, always linked to a challenge. Supports named solvers, AI-suggested candidates, or both. |
| `/vpm-fit-check` | Evaluate solver-challenge fit across strategic, technical, and operational-cultural dimensions. Produces a scored assessment with critical risks and a final recommendation: Advance to pilot / Conditional / Reject. |
| `/vpm-pilot-launch` | Co-design a pilot between a solver and an organization or unit. Generates a complete pilot design document with success criteria, KPIs, timeline, responsibilities, and escalation conditions. Creates blocker entries for critical risks. |
| `/vpm-pilot-review` | Manage the pilot lifecycle after launch: activate the pilot, record progress checks, and close the pilot with a final decision (Scale / Extend / Stop). Creates a decision log entry at close. |
| `/vpm-blocker-check` | Diagnose why a challenge, solver evaluation, or pilot is not progressing. Classifies the blocker type, assesses severity, assigns an owner, and recommends concrete next steps with deadlines. |
| `/vpm-report` | Generate a periodic executive report for a program or a specific batch. Traverses the full hierarchy (or batch scope) to consolidate pipeline status, headline metrics, top advances, open blockers, decisions, learnings, and prioritized next steps. Displayed in conversation; saved to `programs/[program-slug]/reports/` only if operator requests. |

_More commands will be added as the OS evolves._

---

## Session Initialization: `/vpm-init`

When the operator writes `/vpm-init`, Claude must:

1. Scan `programs/` for all subfolders containing `program-context.md`.
2. For each program found, traverse its hierarchy to count active challenges, solvers in evaluation, active pilots, and open blockers.
3. Display a structured dashboard. See `.claude/commands/vpm-init.md` for the full format specification.
4. Prompt the operator to select a program or take an action.

If `programs/` is empty or no programs are found, display:

```
--- VenturePM Dashboard ---

No active programs found.

Run /vpm-setup-program to initialize your first program.
```

---

## Notes for Claude

- Always use the standard vocabulary defined above. Never substitute informal synonyms (e.g., use "solver", not "startup" or "vendor").
- When writing files, use the slug conventions: lowercase, hyphen-separated, no special characters, no accents.
- File paths follow the nested hierarchy: `programs/[program-slug]/orgs/[org-slug]/` — never flat.
- Solvers are evaluation artefacts. Never create a solver profile outside of `/vpm-scout`. Never create a solver without a linked challenge.
- Operational subdirectories (`challenges/`, `solvers/`, `pilots/`, `blockers/`, `decisions/`) exist only at the lowest active level — unit if it exists, otherwise organization.
- When an organization is set up, always ask whether it has units (`has_units: true/false`) before creating its folder structure.
- If a unit is added to an org that was previously set up without units (`has_units: false`): (1) move all existing files from the org's operational subdirectories (`challenges/`, `solvers/`, `pilots/`, `blockers/`, `decisions/`) into the new unit's corresponding folders, (2) confirm with the operator that the migration is complete and correct, (3) then remove the org's now-empty operational subdirectories, (4) update `org-context.md` to `has_units: true`. Never delete before migrating.
- Entity context can be collected via three sources: manual input, document import, or web search from verified public sources. All three are valid in `/vpm-setup-program`, `/vpm-setup-batch`, `/vpm-setup-org`, and `/vpm-setup-unit`.
- When a blocker is mentioned, always prompt for classification, owner, and status.
- When a decision is made (advance, reject, scale, stop), always create a decision log entry.
- Keep context files as the source of truth. Do not store state only in conversation memory.
- This OS is generic. Never hardcode assumptions about a specific industry, company, or program.

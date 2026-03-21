# VenturePM — CLAUDE.md

**Author:** David Mora — [github.com/davicomoran](https://github.com/davicomoran)
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

VenturePM uses a four-level hierarchy. `program` and `organization` are required. `batch` and `unit` are optional but unlock additional scoping and context inheritance.

```
program
└── batch (optional)
    └── organization
        └── unit (optional)
```

| Level | Required | Definition |
|---|---|---|
| `program` | Yes | The top-level innovation initiative or corporate group. Has no parent. |
| `batch` | No | A specific instance or cohort of a program, with its own context, rules, and conditions. Inherits from the parent program but can override its own parameters. |
| `organization` | Yes | A company or entity participating in a batch or directly in a program — as a solver, partner, or internal team. |
| `unit` | No | A sub-team or business unit within an organization that owns specific challenges. |

Challenges are always assigned to the lowest active level in the hierarchy.

---

## Standard Vocabulary

All entities, statuses, and commands use English regardless of the operator's language. This ensures consistency across documentation, exports, and integrations.

| Term | Definition |
|---|---|
| `program` | The top-level managed innovation initiative operating within VenturePM. |
| `batch` | A specific cohort or instance of a program with its own context and rules. Inherits from its parent program. |
| `organization` | Any company or entity participating in a batch or program (startup, corporate unit, or partner). |
| `unit` | A sub-division of an organization that owns specific challenges or pilots. |
| `solver` | An organization (typically a startup) that proposes a solution to a challenge. |
| `challenge` | A specific problem statement published by the program to attract solvers. |
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

Every entity — program, batch, organization, or unit — is represented as a folder under `programs/`. Each folder contains a `program-context.md` file with four required fields and its own set of operational subdirectories.

```
venturepm/
├── CLAUDE.md                        # This file — OS definition and operating manual
├── README.md                        # Public-facing introduction
├── programs/                        # One folder per entity (program, batch, org, unit)
│   └── [entity-slug]/
│       ├── program-context.md       # Required. Fields: name, type, parent, mode
│       │                            #   name:   Human-readable entity name
│       │                            #   type:   program / batch / organization / unit
│       │                            #   parent: slug of parent entity (null if top-level)
│       │                            #   mode:   venture-client / venture-building / hybrid
│       ├── challenges/              # One file per challenge owned by this entity
│       │   └── [challenge-slug].md
│       ├── solvers/                 # One file per solver evaluated against a challenge
│       │   └── [solver-slug].md
│       ├── pilots/                  # One file per active or completed pilot
│       │   └── [pilot-slug].md
│       ├── blockers/                # Open and resolved blockers
│       │   └── [blocker-slug].md
│       └── decisions/               # Decision log entries and period reports
│           └── [YYYY-MM-DD]-[slug].md
├── templates/                       # Reusable templates for each entity type
│   ├── program.md
│   ├── challenge.md
│   ├── solver.md
│   ├── pilot.md
│   ├── blocker.md
│   └── decision.md
└── docs/                            # Methodology documentation and guides
```

---

## Entity Types & Linking

### How entities are linked

Every entity in VenturePM is stored as a folder under `programs/`. The `parent` field in `program-context.md` links an entity to its parent in the hierarchy. This creates a traversable graph that Claude uses to scope challenges, inherit context, and generate reports.

```
program-context.md (batch)
  name:   Cohort 2026-Q1
  type:   batch
  parent: global-innovation-sprint   ← slug of parent program
  mode:   venture-client
```

A `program` entity has `parent: null`. All other types must declare a parent.

---

### Inheritance rule

A `batch` inherits the mode, governance, and success metrics of its parent `program` by default. It may override any of these by declaring its own values in `program-context.md`. Overrides apply only within that batch and do not propagate upward.

An `organization` inherits context from whichever entity it is directly parented to — a `batch` if one exists, or a `program` if the batch level is skipped.

A `unit` always inherits from its parent `organization`.

---

### Challenge assignment rule

Challenges are always assigned to the **lowest active level** in the hierarchy. This means:

- If a `unit` exists and is active → challenges are assigned to the unit.
- If no `unit` exists → challenges are assigned to the `organization`.
- If no `organization` exists below a `batch` → challenges are assigned to the batch.

This rule ensures that challenge ownership is as specific as possible, with a clear accountable owner at every level.

---

### Hierarchy examples

**Example A — Venture Client Program (flat structure)**

A corporate innovation unit runs a venture client program. Organizations participate directly — no batch cohorts are needed.

```
program: corporate-innovation-unit   (type: program, parent: null)
  └── organization: acme-logistics   (type: organization, parent: corporate-innovation-unit)
        └── unit: last-mile-ops      (type: unit, parent: acme-logistics)
```

Challenges are assigned to `last-mile-ops`. If the unit did not exist, they would be assigned to `acme-logistics`.

---

**Example B — Corporate Venture Capital (multi-batch structure)**

A CVC group runs multiple investment cohorts. Each cohort (batch) has its own portfolio companies and rules, but shares the group's overarching mode and governance.

```
program: cvc-group                   (type: program, parent: null)
  └── batch: cohort-2026-q1          (type: batch, parent: cvc-group)
        └── organization: startup-x  (type: organization, parent: cohort-2026-q1)
  └── batch: cohort-2025-q3          (type: batch, parent: cvc-group)
        └── organization: startup-y  (type: organization, parent: cohort-2025-q3)
```

Each batch maintains its own challenges, pilots, and blockers. The `cvc-group` program level is used for cross-batch reporting via `/vpm-report`.

---

## Command Reference

All VenturePM commands use the `vpm-` prefix.

| Command | Description |
|---|---|
| `/vpm-init` | Initialize session: show dashboard with active programs and pipeline status. |
| `/vpm-setup-program` | Initialize a new program: collect details, create folder structure, and generate program-context.md. |
| `/vpm-new-command` | Create a new VenturePM command: generate the command file with standard structure and register it in CLAUDE.md. |
| `/vpm-org-setup` | Onboard a new organization into an active program and generate a full organization profile. |
| `/vpm-challenge-workshop` | Facilitate challenge identification, classification by innovation horizon, and prioritization by feasibility × impact for an organization. |
| `/vpm-scout` | Source and evaluate solvers for a challenge, generating structured profiles with strategic, technical, and operational-cultural fit assessment. |
| `/vpm-fit-check` | Evaluate solver-challenge fit across strategic, technical, and operational-cultural dimensions. Produces a scored assessment with critical risks and a final recommendation: Advance to pilot / Conditional / Reject. |
| `/vpm-pilot-launch` | Co-design a pilot between a solver and an organization. Generates a complete pilot design document with success criteria, KPIs, timeline, responsibilities, and escalation conditions. Creates blocker entries for critical risks. |
| `/vpm-blocker-check` | Diagnose why a challenge, solver evaluation, or pilot is not progressing. Classifies the blocker type, assesses severity, assigns an owner, and recommends concrete next steps with deadlines. |
| `/vpm-report` | Generate a periodic executive report for a program. Summarizes pipeline status, headline metrics, top advances, open blockers, decisions, learnings, and prioritized next steps for the next cycle. |
| `/vpm-import-program` | Load program context from an existing document (PDF, Word, or plain text). Extracts structured fields, marks unknowns as [TO CONFIRM], and generates the entity file and folder structure. Supports all four entity types. |

_More commands will be added as the OS evolves._

---

## Session Initialization: `/vpm-init`

When the operator writes `/vpm-init`, Claude must:

1. Scan the `programs/` directory for all active programs.
2. For each program, read `program.md` and summarize:
   - Program name and mode (`venture-client`, `venture-building`, `hybrid`)
   - Active challenges (count and names)
   - Solvers in evaluation (count)
   - Pilots in progress (count and status)
   - Open blockers (count and highest-severity types)
3. Display a structured dashboard in this format:

```
--- VenturePM Dashboard ---

Programs active: N

[Program Name] — Mode: venture-client
  Challenges:  X active
  Evaluation:  Y solvers
  Pilots:      Z in progress
  Blockers:    N open (types: political, budget)

[Program Name] — Mode: hybrid
  Challenges:  X active
  Evaluation:  Y solvers
  Pilots:      Z in progress
  Blockers:    0 open

---
What would you like to work on?
```

4. Prompt the operator to select a program or take an action.

If `programs/` is empty or does not exist, display:

```
--- VenturePM Dashboard ---

No active programs found.

Run /vpm-new-program to initialize your first program.
```

---

## Notes for Claude

- Always use the standard vocabulary defined above. Never substitute informal synonyms (e.g., use "solver", not "startup" or "vendor").
- When writing files, use the slug conventions: lowercase, hyphen-separated, no special characters.
- When a blocker is mentioned, always prompt for classification, owner, and status.
- When a decision is made (advance, reject, scale, stop), always create a decision log entry.
- Keep program files as the source of truth. Do not store state only in conversation memory.
- This OS is generic. Never hardcode assumptions about a specific industry, company, or program.

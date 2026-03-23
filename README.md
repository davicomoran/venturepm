# VenturePM

**The open-source OS for innovation programs.**
**Methodology-first. AI-operated. Built to run.**

---

VenturePM is a complete methodology, vocabulary, and pipeline for running innovation programs — codified as structured files and executable commands, operated today through Claude as an AI co-pilot.

It is built for venture studios, innovation teams, and any organization that systematically works with startups or builds new ventures internally. No third-party dependency, no lock-in. The methodology belongs to whoever runs it.

---

## Why VenturePM

The best product managers don't just move faster — they operate at a different level. With the right tools, context always loaded, and AI handling the structure, one PM can run what used to take a team.

VenturePM applies that same logic to the people who run innovation programs.

A program manager juggles challenge workshops, solver pipelines, fit evaluations, pilot designs, and executive reporting — often across multiple organizations at once. Most of that work is structured and repeatable. VenturePM gives it a home: a methodology, a file system, and an AI co-pilot that knows the process, remembers the context, and takes structured action at every step.

The result is a program manager who runs more cycles, makes better-documented decisions, and carries institutional knowledge that doesn't walk out the door.

VenturePM is different:

- **Methodology-first.** The process is the product. The pipeline, vocabulary, and decision logic are defined independently — Claude is the current runtime, not the boundary of what this can become.
- **File-native.** Program state lives in Markdown files you own, version, and audit. No third-party dependency, no lock-in.
- **AI-operated.** Claude reads your program context, applies the methodology, and takes structured action — not just chat.
- **Open.** Fork it, extend it, adapt it. The OS belongs to whoever runs it.

---

## How it works

VenturePM has three components that work together:

### 1. `CLAUDE.md` — the brain

The operating manual. It defines the methodology, vocabulary, hierarchy, pipeline phases, blocker taxonomy, and all behavioral rules. Claude reads this at the start of every session and operates within it. You never re-explain the methodology — it's always there.

### 2. `/vpm-` commands — the actions

Structured commands that drive the program forward. Each command maps to a phase of the pipeline: setting up a program, running a challenge workshop, scouting solvers, evaluating fit, launching pilots, diagnosing blockers, generating reports. Commands are deterministic — they follow defined steps, produce defined outputs, and write results to the right files.

### 3. `programs/` — the living memory

One folder per entity. Every program, batch, organization, unit, challenge, solver, pilot, blocker, and decision lives here as a Markdown file. Claude reads these files to understand current state. When you run a command, the output is written back to the filesystem. The program memory grows with every session.

---

## Quick start

**Requirements:** [Claude Code](https://claude.ai/code) with file access enabled. Requires a Claude.ai Pro or Max subscription.

```bash
# 1. Clone the repository
git clone https://github.com/davicomoran/venturepm.git
cd venturepm

# 2. Open the project in Claude Code
claude .

# 3. Initialize your session
/vpm-init

# 4. Set up your program structure
/vpm-setup-program
/vpm-setup-batch        # optional — if your program runs cohorts
/vpm-setup-org
/vpm-setup-unit         # optional — if the org has sub-teams

# 5. Start running the pipeline
/vpm-challenge-workshop
/vpm-scout
```

That's it. VenturePM will guide you through the rest.

---

## Command reference

All commands use the `/vpm-` prefix.

| Command | Phase | Description |
|---|---|---|
| `/vpm-init` | Any | Initialize session. Display dashboard with OS health and program pipeline status. |
| `/vpm-setup-program` | Setup | Initialize a new program. Supports manual entry, document import, or web search. Creates `program-context.md`. |
| `/vpm-setup-batch` | Setup | Set up a cohort within a program. Creates `batch-context.md`. |
| `/vpm-setup-org` | Setup | Onboard an organization into a program or batch. Registers it in `batch-context.md` and creates the full org folder structure. |
| `/vpm-setup-unit` | Setup | Set up a sub-team or business unit within an organization. Creates operational subdirectories at the unit level. |
| `/vpm-challenge-workshop` | Challenges | Facilitate challenge identification and prioritization. Supports interactive workshop (Mode A) or document import (Mode B — PDF, Miro export, plain text). Updates org context with active challenges. |
| `/vpm-scout` | Scouting | Source and evaluate solvers for a challenge. Generates structured profiles with fit assessment. Advances challenge status to `in-evaluation`. |
| `/vpm-fit-check` | Evaluation | Evaluate solver-challenge fit across strategic, technical, and operational-cultural dimensions. Output: scored assessment with recommendation (Advance / Conditional / Reject). |
| `/vpm-pilot-launch` | Pilots | Co-design a pilot. Generates a complete pilot design document with KPIs, timeline, responsibilities, and escalation conditions. Advances challenge status to `in-pilot`. |
| `/vpm-pilot-review` | Pilots | Manage the pilot lifecycle after launch: activate, record progress checks, and close with a Scale / Extend / Stop decision. Appends to the decision log at close. |
| `/vpm-blocker-check` | Any | Diagnose why something is stalled. Classifies blocker type, assesses severity, assigns owner, recommends next steps. |
| `/vpm-report` | Any | Generate a periodic executive report. Supports full-program or batch-scoped reports. Summarizes pipeline status, advances, blockers, decisions, and next steps. Saved to `programs/[slug]/reports/` on request. |
| `/vpm-new-command` | OS | Create a new VenturePM command with standard structure and register it in `CLAUDE.md`. |

---

## Program hierarchy

VenturePM organizes programs using a four-level hierarchy. `program` and `organization` are required. `batch` and `unit` are optional.

```
program
└── batch (optional)
    └── organization
        └── unit (optional)
```

| Level | Required | Description |
|---|---|---|
| `program` | Yes | The top-level innovation initiative. Has no parent. |
| `batch` | No | A specific cohort or instance of a program. Inherits from the parent program but can override its own parameters. |
| `organization` | Yes | The challenger entity — a company, NGO, or any organization that defines challenges and evaluates solvers. Always the problem owner, never the solution provider. |
| `unit` | No | A sub-team or business unit within an organization that owns specific challenges. |

Challenges are always assigned to the lowest active level in the hierarchy.

---

## Methodology

VenturePM is grounded in Venture Client theory and innovation practice. It draws from direct program experience and the following references:

- Gimmy, G. & Moosmayer, D. — *The Venture Client Model* (foundational framework for corporate-startup collaboration through real procurement and pilots)
- Viki, T., Toma, D. & Gons, E. — *The Corporate Startup* (innovation pipeline design and governance in large organizations)
- Ries, E. — *The Lean Startup* (hypothesis-driven pilots and validated learning)

The methodology is intentionally generic. It does not encode assumptions about any specific industry, geography, or institution.

---

## Roadmap

**What's working now (v0.3)**

The full venture-client pipeline is operational and end-to-end tested:

- All 13 commands implemented and validated
- Complete pipeline tracking: challenges advance through `active → in-evaluation → in-pilot → completed`
- Centralized decision and blocker logs per challenge
- Batch-scoped reporting with optional file save
- Context inference between chained commands
- Scoped to venture-client mode — no false promises on unimplemented modes

**What's next**

- **Venture Building mode** — extend the pipeline for internal venture creation: hypothesis validation, team formation, and spin-off decisions.
- **VenturePM for Accelerators** — a separate product for managing accelerator pipelines, built on the same foundation.

The roadmap beyond this is intentionally open. VenturePM's direction should be shaped by the people running real programs on it — not by assumptions made in advance.

Have ideas? [Open an issue](https://github.com/davicomoran/venturepm/issues) or [start a discussion](https://github.com/davicomoran/venturepm/discussions). The best features will come from operators who use it.

---

## Contributing

VenturePM is open source and welcomes contributions at every level:

- **Methodology:** Improve the operating principles, phase definitions, or blocker taxonomy.
- **Commands:** Propose or build new `/vpm-` commands for gaps in the pipeline.
- **Templates:** Help define the standard structure for entity files.
- **Documentation:** Clarify the guides and make the methodology more accessible.
- **Case studies:** Share how you've used VenturePM in a real program (anonymized is fine).

To contribute: fork the repo, make your changes, and open a pull request with a clear description of what you changed and why.

---

## Author

**David Moran** — [github.com/davicomoran](https://github.com/davicomoran)

---

## License

MIT — use it, fork it, build on it.

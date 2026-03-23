# /vpm-init

Initializes a VenturePM session. Displays a complete project status dashboard: OS health (commands and infrastructure), the full operational cycle, active programs with pipeline status, and open blockers. Entry point for every working session.

---

## Step 1 — Scan OS infrastructure

Check for the presence of the following files and directories. Record each as present or missing:

**Commands** — check `.claude/commands/` for each expected file:
- `vpm-init.md`
- `vpm-setup-program.md`
- `vpm-setup-batch.md`
- `vpm-setup-org.md`
- `vpm-setup-unit.md`
- `vpm-new-command.md`
- `vpm-challenge-workshop.md`
- `vpm-scout.md`
- `vpm-fit-check.md`
- `vpm-pilot-launch.md`
- `vpm-pilot-review.md`
- `vpm-blocker-check.md`
- `vpm-report.md`

**Infrastructure** — check for:
- `README.md` (root)
- `docs/` directory (any content)

---

## Step 2 — Scan programs directory

Check whether `programs/` exists and contains any subdirectories.

For each subfolder found under `programs/`, look for a `program-context.md` file. Read it and confirm `type: program`. Only include entities where `type: program` in the Active Programs section.

For each program found, extract:
- Program name and mode
- Program status (active / paused / completed)

Then count pipeline entities per program by scanning the full nested hierarchy:

**Scanning rule:** For each program, recursively find all `challenges/`, `solvers/`, `pilots/`, and `blockers/` directories anywhere within the program's folder tree. These exist at org level (when `has_units: false`) or unit level (when `has_units: true`). The hierarchy can be:

```
programs/[program]/orgs/[org]/challenges/
programs/[program]/orgs/[org]/units/[unit]/challenges/
programs/[program]/batches/[batch]/orgs/[org]/challenges/
programs/[program]/batches/[batch]/orgs/[org]/units/[unit]/challenges/
```

**Challenges** — in all `challenges/` directories found: count `.md` files = active challenges

**Solvers** — in all `solvers/` directories found: count files not ending in `-fit.md` = solver profiles; count those with `Status: in-evaluation`

**Pilots** — in all `pilots/` directories found: count files with `Status: active` or `Status: in-design`

**Blockers** — in all `blockers/` directories found: count files with `Status: open` or `Status: in-progress`; collect their `Type` values

If a directory does not exist, treat its count as 0.

---

## Step 3 — Display full dashboard

Display the complete dashboard in this format:

```
════════════════════════════════════════
  VENTUREPM — Session Dashboard
  [YYYY-MM-DD]
════════════════════════════════════════

── OS COMMANDS ─────────────────────────

  /vpm-init               [present / MISSING]
  /vpm-setup-program      [present / MISSING]
  /vpm-setup-batch        [present / MISSING]
  /vpm-setup-org          [present / MISSING]
  /vpm-setup-unit         [present / MISSING]
  /vpm-new-command        [present / MISSING]
  /vpm-challenge-workshop [present / MISSING]
  /vpm-scout              [present / MISSING]
  /vpm-fit-check          [present / MISSING]
  /vpm-pilot-launch       [present / MISSING]
  /vpm-pilot-review       [present / MISSING]
  /vpm-blocker-check      [present / MISSING]
  /vpm-report             [present / MISSING]

── PROJECT INFRASTRUCTURE ──────────────

  README.md              [present / missing]
  docs/                  [present / missing]

── OPERATIONAL CYCLE ───────────────────

  /vpm-setup-program
    └─ /vpm-setup-batch  (optional)
         └─ /vpm-setup-org
              └─ /vpm-setup-unit  (optional)
                   └─ /vpm-challenge-workshop
                        └─ /vpm-scout
                             └─ /vpm-fit-check
                                  └─ /vpm-pilot-launch
                                       └─ /vpm-pilot-review
                                            ├─ /vpm-blocker-check  (any phase)
                                            └─ /vpm-report
                                                 └─ /vpm-challenge-workshop (next cycle)

── ACTIVE PROGRAMS ─────────────────────

  Programs active: [N]

  [Program Name] — [venture-client / venture-building / hybrid]
    Challenges:  [N] active
    Evaluation:  [N] solvers in evaluation
    Pilots:      [N] in progress
    Blockers:    [N] open[, types: type1, type2]

  [Program Name] — [mode]
    Challenges:  [N] active
    Evaluation:  [N] solvers in evaluation
    Pilots:      [N] in progress
    Blockers:    [N] open

════════════════════════════════════════
What would you like to work on?
```

**Formatting rules:**

- Use today's date in the header.
- In the OS Commands section, mark each file `present` if it exists in `.claude/commands/`, or `MISSING` (uppercase) if not.
- In the Project Infrastructure section, mark each item `present` if it exists, or `missing` (lowercase) if not.
- In the Active Programs section, list programs alphabetically by slug. Count only `active` programs in the headline; include `paused` and `completed` programs with their label appended — e.g., `venture-client (paused)`.
- Only include `types:` on the Blockers line if there is at least one open blocker.
- Pipeline counts are aggregated across all orgs, batches, and units within the program.
- If `programs/` is empty or does not exist, replace the Active Programs section with:

```
── ACTIVE PROGRAMS ─────────────────────

  No programs found.
  Run /vpm-setup-program to initialize your first program.
```

---

## Step 4 — Prompt for next action

After displaying the dashboard, wait for the operator's response. Based on their answer, suggest or invoke the appropriate command:

| If the operator wants to… | Suggest |
|---|---|
| Start a new program | `/vpm-setup-program` |
| Add a cohort to a program | `/vpm-setup-batch` |
| Add an organization to a program or batch | `/vpm-setup-org` |
| Add a unit to an organization | `/vpm-setup-unit` |
| Define or prioritize challenges | `/vpm-challenge-workshop` |
| Scout solvers for a challenge | `/vpm-scout` |
| Evaluate a solver against a challenge | `/vpm-fit-check` |
| Design or launch a pilot | `/vpm-pilot-launch` |
| Activate, check, or close a pilot | `/vpm-pilot-review` |
| Investigate why something is stalled | `/vpm-blocker-check` |
| Generate a program report | `/vpm-report` |
| Create a new command | `/vpm-new-command` |
| Review the dashboard again | `/vpm-init` |

---

## Quality criteria

- [ ] OS infrastructure was scanned from actual files — not assumed from memory.
- [ ] All 13 expected commands are listed with accurate present/MISSING status.
- [ ] Both infrastructure items (README.md and docs/) are listed with accurate present/missing status.
- [ ] The operational cycle is displayed as a visual tree including setup-batch and setup-unit.
- [ ] Pipeline counts traverse the full nested hierarchy — not just program-level paths.
- [ ] Blocker types are listed only when at least one open blocker exists.
- [ ] Programs with paused or completed status are shown but labeled.
- [ ] Today's date appears in the dashboard header.

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | — | `/vpm-init` is the entry point. No command precedes it. |
| Followed by | Any command | The dashboard surfaces what needs attention — the operator selects the next command based on program state and infrastructure gaps. |
| Recommended after | Any significant action | Re-running `/vpm-init` after completing a command gives an updated view of the full project state. |

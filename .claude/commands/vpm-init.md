# /vpm-init

Initializes a VenturePM session. Displays a complete project status dashboard: OS health (commands and infrastructure), the full operational cycle, active programs with pipeline status, and open blockers. Entry point for every working session.

---

## Step 1 — Scan OS infrastructure

Check for the presence of the following files and directories. Record each as present or missing:

**Commands** — check `.claude/commands/` for each expected file:
- `vpm-init.md`
- `vpm-setup-program.md`
- `vpm-import-program.md`
- `vpm-new-command.md`
- `vpm-org-setup.md`
- `vpm-challenge-workshop.md`
- `vpm-scout.md`
- `vpm-fit-check.md`
- `vpm-pilot-launch.md`
- `vpm-blocker-check.md`
- `vpm-report.md`

**Infrastructure** — check for:
- `README.md` (root)
- `templates/program.md`
- `templates/challenge.md`
- `templates/solver.md`
- `templates/pilot.md`
- `templates/blocker.md`
- `templates/decision.md`
- `docs/` directory (any content)

---

## Step 2 — Scan programs directory

Check whether `programs/` exists and contains any subdirectories.

For each program found, read `programs/[program-slug]/program-context.md` and extract:
- Program name and mode
- Program status (active / paused / completed)

Then count pipeline entities per program:

**Challenges** (`challenges/`) — count `.md` files = active challenges

**Solvers** (`solvers/`) — count files not ending in `-fit.md` = solver profiles; count those with `Status: in-evaluation`

**Pilots** (`pilots/`) — count files with `Status: active` or `Status: in-design`

**Blockers** (`blockers/`) — count files with `Status: open` or `Status: in-progress`; collect their `Type` values

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
  /vpm-import-program     [present / MISSING]
  /vpm-new-command        [present / MISSING]
  /vpm-org-setup          [present / MISSING]
  /vpm-challenge-workshop [present / MISSING]
  /vpm-scout              [present / MISSING]
  /vpm-fit-check          [present / MISSING]
  /vpm-pilot-launch       [present / MISSING]
  /vpm-blocker-check      [present / MISSING]
  /vpm-report             [present / MISSING]

── PROJECT INFRASTRUCTURE ──────────────

  README.md              [present / missing]
  templates/program.md   [present / missing]
  templates/challenge.md [present / missing]
  templates/solver.md    [present / missing]
  templates/pilot.md     [present / missing]
  templates/blocker.md   [present / missing]
  templates/decision.md  [present / missing]
  docs/                  [present / missing]

── OPERATIONAL CYCLE ───────────────────

  /vpm-setup-program  (or /vpm-import-program)
    └─ /vpm-org-setup
         └─ /vpm-challenge-workshop
              └─ /vpm-scout
                   └─ /vpm-fit-check
                        └─ /vpm-pilot-launch
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
- In the Active Programs section, list programs alphabetically by slug. Count only `active` programs in the headline; include `paused` and `completed` programs with their label appended — e.g., `Mode: venture-client (paused)`.
- Only include `types:` on the Blockers line if there is at least one open blocker.
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
| Add an organization to a program | `/vpm-org-setup` |
| Define or prioritize challenges | `/vpm-challenge-workshop` |
| Scout solvers for a challenge | `/vpm-scout` |
| Evaluate a solver against a challenge | `/vpm-fit-check` |
| Design or launch a pilot | `/vpm-pilot-launch` |
| Investigate why something is stalled | `/vpm-blocker-check` |
| Generate a program report | `/vpm-report` |
| Create a new command | `/vpm-new-command` |
| Review the dashboard again | `/vpm-init` |

---

## Quality criteria

- [ ] OS infrastructure was scanned from actual files — not assumed from memory.
- [ ] All 11 expected commands are listed with accurate present/MISSING status.
- [ ] All 8 infrastructure items are listed with accurate present/missing status.
- [ ] The operational cycle is displayed as a visual tree.
- [ ] Program pipeline counts are derived from actual files, not estimated.
- [ ] Blocker types are listed only when at least one open blocker exists.
- [ ] Programs with paused or completed status are shown but labeled.
- [ ] Today's date appears in the dashboard header.

---

## Usage example

```
Operator: /vpm-init

Claude:
════════════════════════════════════════
  VENTUREPM — Session Dashboard
  2026-03-20
════════════════════════════════════════

── OS COMMANDS ─────────────────────────

  /vpm-init               present
  /vpm-setup-program      present
  /vpm-new-command        present
  /vpm-org-setup          present
  /vpm-challenge-workshop present
  /vpm-scout              present
  /vpm-fit-check          present
  /vpm-pilot-launch       present
  /vpm-blocker-check      present
  /vpm-report             present

── PROJECT INFRASTRUCTURE ──────────────

  README.md               missing
  templates/program.md    missing
  templates/challenge.md  missing
  templates/solver.md     missing
  templates/pilot.md      missing
  templates/blocker.md    missing
  templates/decision.md   missing
  docs/                   missing

── OPERATIONAL CYCLE ───────────────────

  /vpm-setup-program  (or /vpm-import-program)
    └─ /vpm-org-setup
         └─ /vpm-challenge-workshop
              └─ /vpm-scout
                   └─ /vpm-fit-check
                        └─ /vpm-pilot-launch
                             ├─ /vpm-blocker-check  (any phase)
                             └─ /vpm-report
                                  └─ /vpm-challenge-workshop (next cycle)

── ACTIVE PROGRAMS ─────────────────────

  No programs found.
  Run /vpm-setup-program to initialize your first program.

════════════════════════════════════════
What would you like to work on?
```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | — | `/vpm-init` is the entry point. No command precedes it. |
| Followed by | Any command | The dashboard surfaces what needs attention — the operator selects the next command based on program state and infrastructure gaps. |
| Recommended after | Any significant action | Re-running `/vpm-init` after completing a command gives an updated view of the full project state. |

# /vpm-init

Initializes a VenturePM session. Displays a complete project status dashboard: OS health (commands), the full operational cycle by phase, active programs with structure and pipeline status, and open blockers. Entry point for every working session.

---

## Step 1 — Scan OS commands

Check for the presence of the following files in `.claude/commands/`. Record each as present or missing:

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

---

## Step 2 — Scan programs directory

Check whether `programs/` exists and contains any subdirectories.

For each subfolder found under `programs/`, look for a `program-context.md` file. Read it and confirm `type: program`. Only include entities where `type: program` in the Active Programs section.

For each program found, extract:
- Program name and mode
- Program status (active / paused / completed)
- Whether the program has batches: count subdirectories under `batches/` and collect their slugs. If no `batches/` directory exists, treat batch count as 0.

Then count pipeline entities per program by scanning the full nested hierarchy:

**Scanning rule:** For each program, recursively find all `challenges/`, `solvers/`, `pilots/`, and `blockers/` directories anywhere within the program's folder tree. These exist at org level (when `has_units: false`) or unit level (when `has_units: true`). The hierarchy can be:

```
programs/[program]/orgs/[org]/challenges/
programs/[program]/orgs/[org]/units/[unit]/challenges/
programs/[program]/batches/[batch]/orgs/[org]/challenges/
programs/[program]/batches/[batch]/orgs/[org]/units/[unit]/challenges/
```

All entity files (challenges, solvers, pilots) now carry a YAML frontmatter block at the top of the file. Use this block — not the Markdown table body — for status detection. Frontmatter fields are lowercase (`status`, `type`, `slug`, etc.).

**Challenges** — in all `challenges/` directories found: count `.md` files where frontmatter `type: challenge` and `status` is not `completed` = active challenges

**Solvers** — in all `solvers/` directories found: count files where frontmatter `type: solver` = solver profiles (this naturally excludes `-fit.md` files which have no such frontmatter); count those where `status: in-evaluation`

**Pilots** — in all `pilots/` directories found: count files where frontmatter `type: pilot` and `status: active` or `status: in-design` = pilots in progress

**Blockers** — in all `blockers/` directories found: for each `[challenge-slug]-blockers.md` file, read the Markdown table and count rows where the `Status` column value is `open` or `in-progress`; collect their `Type` values. Blocker files do not use frontmatter — they are append-only logs.

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

  /vpm-init               [present / MISSING]   Initialize session dashboard
  /vpm-setup-program      [present / MISSING]   Create a new program
  /vpm-setup-batch        [present / MISSING]   Add a batch to a program
  /vpm-setup-org          [present / MISSING]   Add an organization
  /vpm-setup-unit         [present / MISSING]   Add a unit to an org
  /vpm-challenge-workshop [present / MISSING]   Define and prioritize challenges
  /vpm-scout              [present / MISSING]   Source solvers for a challenge
  /vpm-fit-check          [present / MISSING]   Evaluate solver-challenge fit
  /vpm-pilot-launch       [present / MISSING]   Design and launch a pilot
  /vpm-pilot-review       [present / MISSING]   Activate, check, or close a pilot
  /vpm-blocker-check      [present / MISSING]   Diagnose and classify a blocker
  /vpm-report             [present / MISSING]   Generate an executive report
  /vpm-new-command        [present / MISSING]   Create a new OS command

── OPERATIONAL CYCLE ───────────────────

  SETUP       /vpm-setup-program → /vpm-setup-batch (opt)
                → /vpm-setup-org → /vpm-setup-unit (opt)

  CHALLENGES  /vpm-challenge-workshop

  SCOUTING    /vpm-scout

  EVALUATION  /vpm-fit-check

  PILOTS      /vpm-pilot-launch → /vpm-pilot-review

  ANY PHASE   /vpm-blocker-check   /vpm-report

── ACTIVE PROGRAMS ─────────────────────

  Programs active: [N]

  [Program Name] — [venture-client / venture-building / hybrid]
    Batches:     [N]  ([batch-slug-1], [batch-slug-2])
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
- In the OS Commands section, mark each file `present` if it exists in `.claude/commands/`, or `MISSING` (uppercase) if not. Always show the description alongside the status.
- In the Active Programs section, list programs alphabetically by slug. Count only `active` programs in the headline; include `paused` and `completed` programs with their label appended — e.g., `venture-client (paused)`.
- Show the `Batches:` line only if the program has at least one batch. List batch slugs in parentheses. If no batches exist, omit the line entirely.
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

- [ ] OS commands were scanned from actual files — not assumed from memory.
- [ ] All 13 expected commands are listed with accurate present/MISSING status and their description.
- [ ] The operational cycle is displayed as a phase-based layout (not a tree).
- [ ] Pipeline counts traverse the full nested hierarchy — not just program-level paths.
- [ ] Batches line is shown only when the program has at least one batch; omitted otherwise.
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

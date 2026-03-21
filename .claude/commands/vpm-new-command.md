# /vpm-new-command

Automates the creation of new commands in VenturePM. Generates the command file with a standard structure and registers it in the Command Reference table in `CLAUDE.md`.

---

## Step 1 — Collect command details

Ask the operator for all of the following in a single message:

```
To create a new VenturePM command, please provide the following:

1. Command name
   Will be prefixed with vpm-. Use lowercase and hyphens.
   Example: "org-setup" → /vpm-org-setup

2. Purpose
   What problem does this command solve or what process does it automate?
   (1–2 sentences)

3. Input required
   What must the operator provide to run this command?
   List each input field and a brief description.

4. Expected output
   What files are created or updated when this command runs?
   Where are they saved?

5. Connection to other commands
   - What command typically precedes this one?
   - What command typically follows this one?
```

Wait for the operator's response before proceeding.

---

## Step 2 — Derive the command slug

Normalize the command name: lowercase, hyphen-separated, no special characters.

The full command name is: `vpm-[command-name]`
The file path is: `.claude/commands/vpm-[command-name].md`

---

## Step 3 — Generate .claude/commands/vpm-[command-name].md

Create the command file using this standard structure:

```markdown
# /vpm-[command-name]

[Purpose — one or two sentences describing what this command does and why it exists.]

---

## Step 1 — Collect inputs

Ask the operator for the following in a single message:

\```
[List each input field with a short prompt and example.]
\```

Wait for the operator's response before proceeding.

---

## Step 2 — [Action step title]

[Describe the first action taken after inputs are collected: validations, derivations, slug generation, lookups, etc.]

---

## Step 3 — [Action step title]

[Describe the next action: file creation, folder setup, template population, etc.]

---

## Step N — Confirm and suggest next step

After completing all actions, display a confirmation summary:

\```
[Command name] completed.

  [Key field]:  [value]
  [Key field]:  [value]

Files created or updated:
  [path/to/file.md]
  [path/to/folder/]

Next step: run /vpm-[next-command] to [brief description of what comes next].
\```

---

## Output template

[Paste the full markdown template that this command generates, with placeholder values in [brackets].]

---

## Quality criteria

- [ ] All required inputs were collected before any file was created.
- [ ] File paths follow slug conventions: lowercase, hyphen-separated.
- [ ] Generated files use the standard template without hardcoded program or company names.
- [ ] CLAUDE.md Command Reference table was updated.
- [ ] Confirmation summary was shown to the operator.

---

## Usage example

\```
Operator: /vpm-[command-name]

Claude: [Shows the input prompt from Step 1]

Operator: [Provides inputs]

Claude: [Executes steps, creates files, shows confirmation summary]
\```

---

## Connection to other commands

| Relation | Command | Why |
|---|---|---|
| Preceded by | `/vpm-[previous-command]` | [What must exist before this command runs] |
| Followed by | `/vpm-[next-command]` | [What this command enables next] |
```

Replace all placeholder content with the operator's inputs. Remove sections that are not applicable (e.g., if there is no output template, omit that section).

---

## Step 4 — Update CLAUDE.md command table

Add a row to the Command Reference table in `CLAUDE.md`:

```
| `/vpm-[command-name]` | [Purpose — one sentence.] |
```

Insert it after the last existing command row, before the italicized note.

---

## Step 5 — Confirm and suggest next step

Display a confirmation summary:

```
New command created successfully.

  Command:  /vpm-[command-name]
  File:     .claude/commands/vpm-[command-name].md

CLAUDE.md updated:
  Command Reference table — 1 row added.

The command is ready to use. Run /vpm-[command-name] to execute it for the first time.
```

# CLAUDE.md — Executor context for {{PROJECT_NAME}}

> Read this file at the start of every Claude Code session in this repository.
> It onboards you as the Executor for ongoing development work on this project.
> This file follows the project-manifesto framework. See `docs/MANIFESTO.md`.

---

## What this repository is

{{ONE_PARAGRAPH_PROJECT_DESCRIPTION}}

Repository: {{REPO_URL}}

Current state: see `PROJECT.md` for facts (name, stack, phase, endpoints) and
`TODO.md` for active work. See `CHANGELOG.md` for recent history.

## Your role here

You are the **Executor**.

- The **Operator** is {{OPERATOR_NAME_OR_HANDLE}}. All decisions of authorial
  weight belong to the Operator.
- The **Architect** is a Claude.ai chat session (often a separate window the
  Operator keeps open) that plans changes, formulates prompts for you, and
  reviews your step reports via MCP filesystem access.
- You execute explicit prompts and produce step reports. You do not invent
  architecture for this project, and you do not commit without the Operator's
  confirmation.

For the full role definition see `docs/MANIFESTO.md` § 4 "Workflow roles".

## Files to read at session start

Before any working prompt, read:

1. `docs/MANIFESTO.md` — universal workflow rules.
2. `PROJECT.md` — project facts and tooling.
3. `TODO.md` — single source of truth for tasks.
4. The most recent file in `docs/sessions/` — last session's handoff.

Skim only — you are not memorizing. The goal is to know what exists and where.

## Working language

{{WORKING_LANGUAGE_NOTE}}

<!--
Examples:
- "Operator's working language for chat is English. All written file content is
  in English."
- "Operator's working language for chat is Russian. Respond in Russian for
  explanations; written file content stays in English unless the Operator
  explicitly asks for Russian content."
-->

## Commit policy

{{REPO_VISIBILITY_NOTE}}

<!--
Examples:
- "This is a public repository. Commits are visible to the world."
- "This is a private repository. Commits are visible only to the team."
-->

Before committing, always:

1. Pre-flight check (`git status`, `git diff --stat`, `git log -1`,
   `git config user.email`, `git remote -v`, `git branch --show-current`).
   Show output to the Operator.
2. Wait for explicit "go ahead" from the Operator.
3. Stage with `git add -A`. Show the staged file list. If unexpected files
   appear, stop and ask.
4. Write the commit message **to a temporary file** (`/tmp/commit-msg.txt`)
   using `cat > ... << 'EOF'` and commit with `git commit -F /tmp/commit-msg.txt`.
   Never use `git commit -m "..."` with multi-line body or shell metacharacters.
   This is the Shell hygiene rule from `docs/MANIFESTO.md`.
5. After the commit, show `git log -1 --stat`.
6. Create annotated tag (if applicable) with `git tag -a vX.Y.Z -m "..."`.
7. Push branch first, then tag separately. If the first push fails — stop, do
   not retry.
8. Clean up the temp file at the end.

Hard rules:
- Never `git push --force`.
- Never `git reset --hard`.
- Never push without the Operator's explicit go-ahead.

## What you may do without asking

- Read any file in the repository.
- Run read-only git commands (`git status`, `git log`, `git diff`, `git show`).
- Run read-only filesystem inspection.
- Create step reports in `docs/sessions/{date}-{slug}/` per Pack H rules.
- Append to `docs/sessions/{date}.md` daily handoff per manifesto § 2.

## What you must ask before doing

- Modifying `docs/MANIFESTO.md`, `PROJECT.md`, `TODO.md`, `README.md`,
  `CHANGELOG.md`, or any root-level regulatory file.
- Modifying files in `docs/architecture/`, `docs/legal/`, or any folder the
  Operator marks as confidential.
- Any `git` write operation (`add`, `commit`, `tag`, `push`, branch operations).
- Installing or removing dependencies.
- Running build, migration, or anything that mutates state outside the
  repository working tree.
- Network requests outside localhost.

## Step reports (Pack H)

For multi-step tasks (recon → analysis → implementation → verification), use the
step-report flow:

- Create folder `docs/sessions/{YYYY-MM-DD}-{task-slug}/`.
- Write each step as a separate file `NN-{step-name}.md` (zero-padded number).
- Use `templates/step-report.template.md` as the structure.
- Write each file in **one** filesystem operation (atomicity).
- The Architect reads the latest step report via MCP after the Operator gives a
  proceed signal.

### Pausing mid-step

When a multi-step task prompt instructs you to pause for Operator confirmation,
input, or review, write a pause checkpoint **before** printing the gate output
to the terminal. The checkpoint is a file at
`docs/sessions/{date}-{slug}/NN-pause-{descriptor}.md` where `NN` is the parent
step number and `{descriptor}` is a short snake-case slug describing what
triggered the pause (`after-inspection`, `before-destructive-delete`,
`awaiting-decision`). Use the template at
`templates/pause-checkpoint.template.md`. Write the checkpoint in one
filesystem operation (same atomicity rule as step reports). On resume, leave
the checkpoint in place as historical record; the final step report at
`NN-{step-name}.md` subsumes it at step completion.

See `docs/MANIFESTO.md` § 2 "Step reports" for the full protocol, including
semantic triggers (proceed / pause / rollback / close), recon subfolders, and
the short-steps principle.

## Common pitfalls

- **Confusing sandbox `create_file` with MCP `write_file`.** Some Claude Code
  environments have both kinds of file tools. The sandbox versions return
  "success" but write to an isolated container. Always use MCP file tools for
  project files. Verify with `list_directory` if uncertain.
- **`git commit -m` with multi-line body destroys files.** Bash interprets `>`
  and other metacharacters in the message as shell redirects, creating junk
  files in the working directory. Always write commit messages to a temp file
  (Shell hygiene rule, `docs/MANIFESTO.md`).
- **Two-chunk prompts.** When the Architect sends two prompts in one message
  ("Chunk 1" then "Chunk 2"), only one gets executed. Treat the first chunk as
  the active prompt and wait for an explicit "do chunk 2 now".

## When in doubt

Read `docs/MANIFESTO.md` and ask the Operator. The cost of asking is small.

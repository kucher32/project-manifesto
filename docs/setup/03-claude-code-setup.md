# Claude Code setup — Executor

> Configure Claude Code as the Executor for your project.
> Estimated time: 10 minutes.
>
> Prerequisites: complete [`01-prerequisites.md`](./01-prerequisites.md)
> and [`02-claude-ai-setup.md`](./02-claude-ai-setup.md) first.

## What this setup does

Claude Code, like Claude.ai, has no project-aware memory by default. But
unlike Claude.ai, Claude Code reads a file in your project root —
**`CLAUDE.md`** — at the start of every session. This file is the
Executor's onboarding: who it is in the workflow, which roles exist,
what it may do without asking, what requires confirmation, how to write
step reports, how to commit safely.

The manifesto ships a template at `templates/CLAUDE.template.md` with
placeholders for project-specific values. Your job in this setup is to
copy the template to your project root, fill the placeholders, and verify
Claude Code picks it up.

## Step 1 — Copy the template to your project root

From inside your project directory (the one that has your `.git/` folder):

```bash
cp templates/CLAUDE.template.md CLAUDE.md
```

If your project does not yet have `templates/` (because you have not yet
adopted the manifesto into this project), copy from the manifesto repo
you cloned:

```bash
cp /path/to/project-manifesto/templates/CLAUDE.template.md ./CLAUDE.md
```

Either way, after this step your project root contains a `CLAUDE.md`
file with placeholder syntax `{{PLACEHOLDER_NAME}}` in several places.

## Step 2 — Fill the placeholders

Open `CLAUDE.md` in your editor. Look for `{{...}}` markers and replace
each with a value specific to your project. Typical placeholders include:

- `{{PROJECT_NAME}}` — short name of your project, e.g., `my-saas`.
- `{{PROJECT_DESCRIPTION}}` — one-sentence description of what the
  project is.
- `{{WORKING_LANGUAGE}}` — the language you write to AI in (e.g.,
  English, Russian, Spanish). All committed file content stays in
  English unless you note otherwise.
- `{{REPO_URL}}` — your project's git remote URL, if it has one.
- `{{COMMIT_POLICY_NOTES}}` — any project-specific commit rules beyond
  the manifesto defaults (e.g., conventional commits, specific commit
  message format, required co-authors).

The template includes comments — lines starting with `<!--` — that
explain what each placeholder should contain. After filling, you may
remove the comments or keep them as living documentation.

## Step 3 — Decide what Claude Code may do without asking

The template ships with default boundaries (see "What you may do without
asking" and "What you must ask before doing" sections). Review them and
adjust to your comfort level:

- **More permissive** — if you trust the Executor with broader autonomy,
  e.g., allow it to install packages without asking, or to push to
  feature branches automatically.
- **More restrictive** — if you want every git operation gated, e.g.,
  require confirmation before any `git add` or any file creation outside
  `docs/sessions/`.

The defaults are tuned for solo founders working on their own projects.
Adjust as needed.

## Step 4 — Verify Claude Code reads CLAUDE.md

In your project directory, start a fresh Claude Code session:

```bash
claude
```

At the first prompt, ask:

> What does CLAUDE.md in this directory say about your role?

A correctly-onboarded Claude Code summarizes the file accurately — your
role (Executor), the working language, the commit policy, the boundaries.
If Claude Code does not recognize the file or summarizes a generic
template instead of your filled version, check:

- The file is named exactly `CLAUDE.md` (case-sensitive on Linux/macOS).
- The file is in the directory you launched `claude` from.
- The file does not have a `.txt` or other extension appended.

## Step 5 — Verify Shell hygiene awareness

This is critical. Ask Claude Code:

> If I ask you to commit a multi-line message with special characters like > or &, how do you do it?

A correctly-configured Claude Code answers with the temporary-file
pattern from `MANIFESTO.md` § Shell hygiene:

```bash
cat > /tmp/commit-msg.txt << 'EOF'
chore: subject line

Body line 1
Body line 2 with > or other special chars
EOF
git commit -F /tmp/commit-msg.txt
```

If Claude Code answers with `git commit -m "..."` and embedded newlines,
re-check that your CLAUDE.md includes the Shell hygiene section. The
template includes it; if you removed it accidentally, restore it.

## Step 6 — Test a Pack H step

Have your Architect (Claude.ai) formulate a tiny test step prompt —
something like "create a file `docs/sessions/test/00-hello.md` with the
text `hello world` and `Status: Done.`". Paste into Claude Code per
Pack P (slash commands separately, prompt body separately).

Verify after execution:
- The file exists at the path specified.
- It was written in one atomic write (the Executor did not append or
  edit-in-place — single write, single read by Architect).
- Claude Code reported a one-line completion in the terminal, not a
  retelling of the file content.

If any of these fail, the Executor is not following Pack H. Re-check
the CLAUDE.md content; specifically the "Writing a Pack H step report"
section.

After the test, delete the test folder:

```bash
rm -rf docs/sessions/test/
```

## Common issues

### Claude Code does not see CLAUDE.md

You may have launched from a parent directory. Run `pwd` to confirm
your working directory and `ls CLAUDE.md` to confirm the file exists
there.

### Claude Code reads the template, not your filled version

Either your edits did not save, or you accidentally edited
`templates/CLAUDE.template.md` instead of `CLAUDE.md` in the root.
Verify with `diff CLAUDE.md templates/CLAUDE.template.md` — there
should be differences (your filled placeholders).

### Claude Code asks for permission for everything

The boundaries in `CLAUDE.md` are too restrictive. Review § "What you
may do without asking" and add the operations you want as defaults.

### Claude Code commits with `-m` despite Shell hygiene rule

The CLAUDE.md section on commits is being ignored, or your version
does not include it. Two fixes:
1. Verify your CLAUDE.md includes the full commit policy with the
   tempfile pattern.
2. Add a stronger marker at the top of the file: a sentence like
   "CRITICAL: never use `git commit -m` with multi-line content. See
   commit policy below."

## After this setup

Continue to [`04-mcp-setup.md`](./04-mcp-setup.md) to give the Architect
read-access to your step reports via MCP filesystem.

# Troubleshooting

> Known issues, edge cases, and their workarounds. This document grows
> with real use — if you hit a problem not listed here, the fix you
> discover is worth contributing back.

## How to use this document

Entries are organized by symptom, not by component. If something is
not working, search this page for the visible failure (the error
message you see, the unexpected behavior you observe), not for the
tool you think is at fault.

Each entry follows the same shape:

- **Symptom** — what you see.
- **Cause** — why it happens (when known).
- **Workaround** — what to do now.
- **Permanent fix** — what the framework or tools should do (if a
  fix is in progress or planned).

## Setup issues

### Symptom — Claude.ai does not recognize manifesto preferences

You pasted the preferences block into Claude.ai Settings, but a fresh
chat does not behave as the preferences specify (no verification gate,
no copyable prompt blocks, MCP write attempts).

**Cause.** Most commonly: the preferences block was not actually
saved. Anthropic's Settings UI sometimes silently drops changes if the
session times out, or if you navigated away before clicking Save.

**Workaround.** Re-open Settings → Profile → Preferences. Confirm the
block is present verbatim. Click Save. Open a fresh chat (not the
current one) and ask: `manifesto preferences check`. A correctly
configured Architect responds with the nine rule topics.

**Permanent fix.** Anthropic's Settings UI behavior. Outside framework
scope.

### Symptom — Claude Code asks for permission for every operation

You paste a step prompt, and Claude Code prompts you for approval on
every file read, every git command, every directory listing.

**Cause.** `CLAUDE.md` boundaries are too restrictive, or `CLAUDE.md`
is not being read at all.

**Workaround.** Check that `CLAUDE.md` exists in the project root
(`ls CLAUDE.md` from where you launched `claude`). Open it and review
the § "What you may do without asking" section. Add operations you
want as defaults.

**Permanent fix.** If you find yourself repeatedly adjusting CLAUDE.md
boundaries for a new project type, consider proposing an updated
`CLAUDE.template.md`.

### Symptom — MCP filesystem returns "directory not found"

The Architect tries to read a file via MCP and reports the directory
or file is not accessible.

**Cause.** Three common causes:
1. The MCP server is authorized for a different directory than the
   one your project actually lives in (e.g., authorized
   `/home/you/projects/` but the project is in `/mnt/d/work/`).
2. The MCP server is not running.
3. Permission issues — the user the MCP server runs as cannot read
   the path.

**Workaround.** Check the MCP server's configured paths against your
actual project path. Restart the server. Run `ls -la <project-root>`
to verify read permissions.

**Permanent fix.** Future Pack N revision may include an MCP path
verification step in the session-opening prompt.

## Step loop issues

### Symptom — Claude Code rejects the prompt with a slash-command error

You pasted a step prompt and Claude Code shows an error about
`/model` or `/effort` not being a valid command.

**Cause.** Slash commands were embedded in the multi-line prompt
body, not pasted as separate REPL inputs. Claude Code parses each
paste-Enter as one input; slash commands only work as standalone
inputs.

**Workaround.** Split the prompt into three pastes:
1. `/model sonnet` → Enter
2. `/effort low` → Enter
3. The prompt body → Enter

If the Architect's reply embedded slash commands in the prompt body,
ask the Architect to reformulate per Pack P (separate fenced blocks
per slash command).

**Permanent fix.** Pack P enforces separation. Personal Preferences
rule 6 carries this discipline into every new chat.

### Symptom — Architect proceeds to next step without writing a verdict

You signal "готово", and the Architect's reply goes straight into the
next step prompt without a `clean` / `clean with note` / `needs fix`
verdict.

**Cause.** Pack S verification gate bypassed. The Architect read the
report (or did not) and skipped the explicit verdict line.

**Workaround.** Stop the workflow. Reply: "where's the verdict? Pack
S violation." The Architect should re-read the artifact and produce
the verdict explicitly. If this happens repeatedly across sessions,
the preferences are not loading or are insufficient — re-paste them.

**Permanent fix.** Personal Preferences rule 4 + Pack S formal
specification in MANIFESTO.md.

### Symptom — Executor's commit message does not match the change

You read `git log -1` and the commit message describes something
different from what `git show` actually changed.

**Cause.** The Executor wrote a commit message that drifted from the
prompt. Most common with Haiku-tier Executor on routine commits.

**Workaround.** Since the branch is local (or this is your own
private push), `git commit --amend` with the correct message. If the
commit was already pushed, decide whether to force-push (private
remote, allowed) or add a follow-up commit clarifying the discrepancy.

**Permanent fix.** Pack S verification gate should include reading
`git log -1 --stat` as a verifying artifact distinct from the step
report itself. If a divergence between report and commit is caught,
the verdict is `needs fix`.

## Repository issues

### Symptom — Architect operates on the wrong repository

The Architect reads files from `project-manifesto` (public mirror) and
writes confusion, or reads stale content because the private working
repo has the actual current state.

**Cause.** The session-opening prompt was skipped, or its step 1
(verify which repo is active) was not followed. Architect defaults
to whichever path it first encounters in chat.

**Workaround.** Tell the Architect explicitly which path is the
working copy: "работаем в `/path/to/private-repo`". Restart the
session if much work has accumulated against the wrong path.

**Permanent fix.** Session-opening prompt step 1 mandates this check
on every new session.

## Reporting new issues

If you encounter a problem this document does not address:

1. Record the symptom in a `docs/sessions/{date}-{slug}/` incident
   report (Pack S format).
2. If you find a fix, propose a new entry in this file via a normal
   step prompt to your Executor.
3. If the fix involves a manifesto change, draft it in `_drafts/` and
   bring it to a Thinking session.

Real incidents have shaped Pack J, Pack R, Pack S, and the rules in
this document. The framework expects to keep growing this way.

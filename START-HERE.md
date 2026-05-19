# START HERE

> If this is your first time opening `project-manifesto`, read this file
> end-to-end before touching anything else. Estimated time: 10 minutes
> reading + 15 minutes setup = 25 minutes to a working two-window
> Architect + Executor environment.

## What this project is

`project-manifesto` is a workflow framework for solo founders building
technical projects with AI assistance — specifically, with **Claude.ai**
(as the Architect, planning and reviewing) and **Claude Code** (as the
Executor, running file and shell operations). It is a small set of rules,
not a tool: drop it into any new project, configure Claude.ai once, and
your two-window workflow becomes Pack-compliant.

The framework's goal is to eliminate the most common solo-founder failure
modes when working with AI: context loss between sessions, mode-mixing
within one session, AI executing without architectural oversight, and
sensitive information drifting into the wrong tier of confidentiality.

For the full rationale see [`MANIFESTO.md`](./MANIFESTO.md) and
[`RATIONALE.md`](./RATIONALE.md). This file is the operational entry
point; the manifesto is the contract.

## Two-layer enforcement

Architect-side discipline (Pack H step reports, Pack O silent reads, Pack R
pause checkpoints, Pack S verification gates, Pack P copyable prompts, Pack
K-revised per-prompt model selection) cannot be enforced by `MANIFESTO.md`
alone — Claude.ai cannot read the manifesto before a chat begins. The
framework therefore uses two complementary enforcement layers:

- **Layer 1 — Personal Preferences in Claude.ai.** A short imperative block
  pasted once into Settings, auto-applied to every chat in your account.
  See [`docs/setup/02-claude-ai-setup.md`](./docs/setup/02-claude-ai-setup.md).
- **Layer 2 — Session-opening prompt.** A standardized first message you
  paste at the start of every new Architect session, forcing Claude.ai to
  load project context (MANIFESTO, OPERATING, recent session logs) before
  any work begins. The text is in the next section of this file.

If Layer 1 is missed (preferences not yet set, new account, Anthropic UI
change), Layer 2 catches. If Layer 2 is forgotten (rushed session start),
Layer 1 carries the always-on rules. Both together approximate
hard-to-bypass discipline.

## The standardized session-opening prompt

Paste the block below — verbatim — as the **first message** of every new
Claude.ai chat against this project. It is the difference between an
Architect that starts with context and one that improvises until
something breaks.

---BEGIN SESSION-OPENING PROMPT---

I am opening a new Architect session for project-manifesto work. Before
responding to my next message, complete these steps in order, using your
MCP filesystem tools:

1. Read this project's `OPERATING.md` if present, and confirm via the
   Executor running `git remote -v` which repository (public or private)
   is the working copy. State which one and proceed against it only.

2. Read `MANIFESTO.md` end-to-end. Pay particular attention to § 2 "Handoff
   notes" (including Step reports / Pack H, Pause checkpoints / Pack R if
   present), § 4 "Workflow roles" (including the Architect pre-flight check
   block if present), and § Shell hygiene.

3. Read `RATIONALE.md` for design context.

4. Read the most recent 2-3 entries in `CHANGELOG.md`.

5. Read the most recent session log in `docs/sessions/` (typically the file
   matching today's date or the prior workday). If a multi-step task folder
   exists for an unfinished task, read its highest-numbered step file too.

6. Verify your Claude.ai Personal Preferences include the project-manifesto
   block. If they do not — STOP and tell me to install them per
   `docs/setup/02-claude-ai-setup.md` before any work begins. Do not
   proceed on Layer 2 alone; both layers are required.

After all six steps, reply with:

- A one-paragraph summary of where the project is (current version, last
  session's focus, any open questions).
- The repository name you are working against.
- Whether Personal Preferences are set (per step 6).
- What you propose as the first step today.

Do not start any work until I confirm or redirect after reading your
summary.

---END SESSION-OPENING PROMPT---

## The setup files, in reading order

If you have not yet set up your environment, read these in order. Each is
short (5-10 minutes).

1. [`docs/setup/01-prerequisites.md`](./docs/setup/01-prerequisites.md) —
   What you need installed before starting (Claude.ai Pro/Max plan, Claude
   Code, MCP filesystem, local git, optionally GitHub).

2. [`docs/setup/02-claude-ai-setup.md`](./docs/setup/02-claude-ai-setup.md) —
   Personal Preferences block + how to install. **Required**; without this,
   the Architect runs without manifesto rules.

3. [`docs/setup/03-claude-code-setup.md`](./docs/setup/03-claude-code-setup.md) —
   Claude Code as Executor: copy `templates/CLAUDE.template.md` to your
   project root as `CLAUDE.md`, fill placeholders, verify Claude Code
   reads it on session start.

4. [`docs/setup/04-mcp-setup.md`](./docs/setup/04-mcp-setup.md) —
   MCP filesystem configuration so the Architect can read step reports
   directly. Links to Anthropic's official MCP documentation.

5. [`docs/setup/05-workflow-two-windows.md`](./docs/setup/05-workflow-two-windows.md) —
   How to actually work with Claude.ai and Claude Code in two parallel
   windows: the paste-prompt-watch-terminal-signal-continue loop, where
   each role's focus belongs, how the Operator bridges them.

6. [`docs/setup/99-troubleshooting.md`](./docs/setup/99-troubleshooting.md) —
   Known issues and workarounds. Initially short, grows with real use.

After step 5, you have a working environment. Step 6 is reference, not
required reading.

## After setup — daily use

A typical day with this framework:

1. **Open two terminal/window pairs:** one Claude.ai chat (Architect), one
   Claude Code shell (Executor) in your project directory.
2. **Paste the session-opening prompt** (above) into the Claude.ai chat.
   Wait for Architect's summary.
3. **Confirm or redirect.** Tell the Architect what you want to work on
   today.
4. **Step loop.** Architect formulates a step prompt in fenced blocks
   (Pack P). You copy each block separately into Claude Code (Pack K-revised
   slash commands first, prompt body last). Claude Code writes a step
   report. You signal "готово" / "done" / "ok". Architect reads the report
   via MCP (Pack S verification gate), writes a verdict, formulates the
   next step.
5. **At session close**, ask the Architect to close the session. It
   updates `TODO.md` if relevant, writes the daily handoff at
   `docs/sessions/{YYYY-MM-DD}.md`, and summarizes resume points for
   tomorrow.

Multi-day projects accumulate step report folders and daily handoffs in
`docs/sessions/`. Reading them back is how cross-session memory works in
this framework: not by hoping the AI remembers, but by writing files the
AI rereads at the start of each new session.

## When something feels wrong

Three signs to slow down and re-read:

- **Architect proposes a file operation without writing a prompt for
  Executor.** Architect's MCP filesystem access is read-only by convention.
  If Architect says "let me create X", redirect: "формулируй промпт для
  Executor". This is the most common discipline failure.

- **Architect signals "done" or moves to next step without writing a
  verdict.** Pack S requires a written verdict (`clean` / `clean with
  note` / `needs fix` / `escalate`) as the FIRST content of the reply
  after step completion. No verdict = no verification = silent drift.

- **A slash command (`/model`, `/effort`) appears in the same fenced block
  as the prompt body.** Pack P + Pack K-revised require separate blocks
  per slash command, one for the body. Combined = Claude Code system
  error. Re-ask Architect to reformulate.

If any of these happen, the Architect has bypassed the manifesto. This is
the failure mode the framework is designed to prevent — it cannot prevent
it absolutely, but catching it early is the second-best defense after
Layer 1 + Layer 2 enforcement.

## Project conventions for this repository

- Working language: Operator writes in Russian; file content stays in
  English unless explicitly requested otherwise.
- Default branch: `main` (manifesto documentation). Orphan branch
  `landing` exists for the landing page subproject — see
  [`docs/branches.md`](./docs/branches.md).
- Public mirror: [github.com/kucher32/project-manifesto](https://github.com/kucher32/project-manifesto).
  Release-only. All day-to-day work happens in the private working repository.
- License: MIT. See [`LICENSE`](./LICENSE).

## Where the manifesto is going next

The framework is at v0.5.1 (public) with v0.6.0 in active development.
v0.6.0 introduces Pack N (this setup ritual), Pack R (pause checkpoints),
Pack S (Architect verification gate), Pack K-revised (per-prompt model
selection), Pack O (silent step report reads), and Pack P (copyable
prompt blocks). See [`CHANGELOG.md`](./CHANGELOG.md) for full history and
[`_drafts/`](./_drafts/) for in-flight formulations.

The framework evolves through **evidence-based formalization**: each pack
codifies a workflow rule that emerged from documented operational pain.
Pack J came from real-project step-report practice. Pack R came from one
specific unrecoverable pause incident. Pack S came from a day of
Architect discipline failures. The pattern repeats; the manifesto grows
slowly and only where reality demands.

If you adopt this framework and hit a recurring workflow problem the
manifesto does not address, the right action is to document the incident
(in a session log, in a draft formulation), discuss it in a Thinking
session, and propose a new pack. The framework expects to keep growing.

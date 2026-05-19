# Workflow — two windows in practice

> How the Operator, Architect, and Executor actually work together
> during a typical day. Estimated reading time: 15 minutes.
>
> Prerequisites: setup complete through
> [`04-mcp-setup.md`](./04-mcp-setup.md).

## The physical setup

Two windows on your screen, side by side or on two monitors:

- **Left (or first monitor) — Architect.** A browser tab with a
  Claude.ai chat. This is where you talk strategy, planning, and
  review. Personal Preferences are active (per
  [`02-claude-ai-setup.md`](./02-claude-ai-setup.md)).

- **Right (or second monitor) — Executor.** A terminal with Claude
  Code running (`claude` command) in your project directory. `CLAUDE.md`
  is loaded (per
  [`03-claude-code-setup.md`](./03-claude-code-setup.md)).

You — the Operator — sit between them. You read the Architect's
proposals, copy step prompts to the Executor, watch the Executor work,
signal completion back to the Architect, read verdicts, then repeat.

This is **not** an automation. You are the bridge. The two AIs do not
talk to each other directly — by design. Your judgment between them is
what makes the framework safe.

## A typical day

### Opening the session

1. Open both windows. Make sure Personal Preferences are active in the
   Architect window (one quick visual check in Claude.ai Settings is
   enough on most days).
2. In the Architect window, paste the **session-opening prompt** from
   [`START-HERE.md`](../../START-HERE.md). This forces the Architect to
   read the manifesto, the operating guide, the most recent session log,
   and to verify which repository is the working copy.
3. Wait for the Architect's summary — typically 1-3 paragraphs telling
   you: where the project is, what last session ended on, what the
   Architect proposes for today.
4. Confirm, redirect, or refine. "Да, продолжим с N5." or "Нет, давай
   сначала разберём вчерашний баг." or "Покажи мне сначала open
   questions из последнего session log." The Architect adjusts and
   proposes a first concrete step.

### The step loop

This is the core rhythm. Every meaningful unit of work runs through it.

**1. Architect formulates a step prompt.**

The Architect writes one step prompt, structured per Pack P:

- Pre-flight commands as separate fenced blocks, each with one slash
  command (`/model sonnet`, then `/effort low`).
- The prompt body as its own fenced block with `prompt` language hint
  (renders a copy button in Claude.ai UI).
- Inside the body: the step's goal, the exact actions, the expected
  output file path, and the finalization instruction (one-line terminal
  report).

The Architect's reply ends with the prompt blocks. No retelling of
plan, no extra commentary above or below — just the blocks and a short
framing sentence.

**2. Operator copies each block to the Executor.**

You click the copy button on each block in order:

- Copy `/model sonnet`. Paste into Claude Code. Press Enter.
- Copy `/effort low`. Paste into Claude Code. Press Enter.
- Copy the prompt body. Paste into Claude Code. Press Enter.

Three paste-enters per step. Slash commands as separate REPL inputs
(this is non-negotiable — Claude Code rejects slash commands embedded
in multi-line bodies).

**3. Executor works.**

Claude Code reads the prompt, executes the actions described, writes
the step report file to `docs/sessions/{date}-{slug}/NN-step.md`, and
prints a one-line completion in the terminal. You watch the terminal
to confirm completion — but you do not need to read the step report
content. The Architect will read it via MCP.

If the Executor pauses for your input (Pack R), it writes a pause
checkpoint file and prints output to the terminal. You read the
terminal output, decide, and tell the Architect what you decided
(e.g., "scenario (a), proceed").

**4. Operator signals completion.**

When the Executor's one-line "done" message appears, you type one
word in the Architect chat:

> готово

(Or "done", "ok", "next" — any phrase the Architect's preferences
recognize as a completion signal.)

**5. Architect verifies (Pack S).**

The Architect's first action after your signal is to read the step
report via MCP, then read at least one verifying artifact (the file
itself, `git log --stat`, etc.). It then writes a **verdict** — one
of `clean` / `clean with note` / `needs fix` / `escalate` — as the
FIRST content of its reply.

If `clean`, the Architect proceeds to the next step prompt.
If `clean with note`, it notes the divergence (recorded for later
review) and proceeds.
If `needs fix`, it formulates a corrective step and the loop repeats
at step 1 with the corrective prompt.
If `escalate`, the Architect stops and asks you to decide model
escalation or step reformulation.

**6. Repeat.**

Each step is small and focused (Pack J — short steps). A typical
multi-step task is 5-15 steps. Each step takes 2-10 minutes of
Executor time plus a few seconds of Architect verification.

### Closing the session

When you are ready to stop for the day (or when you switch session
type — Thinking to Coding, etc.), type one of the close signals
recognized in your project's `PROJECT.md` (default in this manifesto:
"закрой сессию" or "close the session").

The Architect:
- Asks the Executor (via one final prompt) to update `TODO.md` and
  write the daily handoff at `docs/sessions/{YYYY-MM-DD}.md` per
  `MANIFESTO.md` § 2.
- Reads the handoff via MCP, verifies it captures Done / Next / any
  Decisions and Open questions.
- Tells you the session is closed and reminds you of the resume points
  for tomorrow.

After this, you may close the Claude.ai tab. The terminal can also
close. Tomorrow's session starts fresh, with the daily handoff as the
restart point.

## Common operational patterns

### Two session types in one day

A day may contain a Thinking session in the morning and a Coding
session in the afternoon (or any combination). Each is a separate
session per Pack C — different Architect chat windows or the same
window with an explicit type switch. Each gets its own handoff at
session close.

Pack A § 2 supports two handoffs per day: name them by date plus
suffix (e.g., `2026-05-19.md` for the morning, `2026-05-19-session-2.md`
for the afternoon).

### Rate limit mid-step

If your Claude.ai plan hits its rate limit mid-step:

- The Executor's step is already running — it finishes whether you
  can talk to the Architect or not.
- You read the resulting step report yourself (Pack S degrades to
  manual review for that step).
- When rate limits reset, paste the step report into the Architect
  chat with: "verify this step, here is the report I read manually."
  The Architect picks up where it left off.

A rate limit is not a session boundary. It is a pause. The handoff
discipline keeps state on disk; the chat reconnects to the same state.

### The Architect makes a mistake

The Architect bypasses Pack S, proposes a write through MCP, embeds
slash commands in the prompt body, forgets to verify. These are
the failure modes the framework is designed to surface, not to
prevent absolutely.

When you notice:

- Call it out directly. "Это нарушение Pack S — где verdict?" or
  "MCP write — это работа Executor'а, формулируй промпт."
- The Architect should acknowledge, correct itself in the next turn,
  and the workflow continues.
- If a pattern recurs across sessions, that is a signal to write a
  Pack S-style incident report and consider a new rule.

Catching the Architect mid-bypass is the second-best defense after
the preferences and session-opening prompt. It is also evidence for
the framework's ongoing evolution.

### The Executor makes a mistake

The Executor writes content that diverges from the prompt (renames
slightly, sums up where the prompt said verbatim, commits with a
message that does not match the change). Pack S verification catches
many of these because the Architect reads the artifact, not just the
report. When you spot one before the Architect does — flag it. The
incident becomes evidence for Pack S formalization.

### Working on a different branch

If your project uses orphan branches (e.g., a separate `landing`
branch with isolated code), the workflow stays the same but the
Operator manages branch switching in the Executor's terminal.

Step reports always live on `main` (or whichever branch holds
`docs/sessions/`). Code work happens on the feature/orphan branch.
Two terminals, one checked out on each branch, is the common
pattern — see `docs/branches.md` if present for branch-model
conventions.

## What this is NOT

- **Not an agent loop.** The two AIs do not talk to each other.
  You bridge them. The bridge is intentional friction; it keeps
  authorial control in your hands (and supports the
  human-authorship argument in `RATIONALE.md`).
- **Not a chat-only workflow.** Files matter. Step reports, pause
  checkpoints, session logs — all of these are on disk first, in
  the chat second. The disk is the source of truth.
- **Not parallel.** Steps run sequentially. The Architect waits
  for the Executor. The Executor waits for prompts. Parallelism
  is a future Pack, not v0.6.0.
- **Not autonomous.** Every meaningful decision passes through the
  Operator. The framework is designed for solo founders who want
  AI leverage without ceding judgment.

## When the workflow feels heavy

Some days the discipline feels like overhead — many small steps,
many verdicts, many pause-and-resume cycles. This is the cost of the
audit trail and the role separation.

The benefit shows up:
- On long projects (week 4 of a project — you can read `docs/sessions/`
  and reconstruct everything).
- After mistakes (you can find the exact step where it went wrong).
- When you hand the project to someone else (the session logs are
  the onboarding).
- When you argue authorship (the trail shows human direction).

For short throwaway scripts, the framework is overkill. Use it where
it pays off; use plain chat where it does not. The judgment of when
to apply which mode is itself an authorial decision the Operator
keeps.

## After this document

You have a complete operational picture. Continue to
[`99-troubleshooting.md`](./99-troubleshooting.md) for known issues
and edge cases, or return to [`START-HERE.md`](../../START-HERE.md)
and begin your first session.

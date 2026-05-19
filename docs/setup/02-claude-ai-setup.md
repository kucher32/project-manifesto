# Claude.ai setup — Architect

> One-time setup of Claude.ai as the Architect for project-manifesto work
> (and any project using this manifesto). Estimated time: 5 minutes.
>
> If you have not yet read `MANIFESTO.md` § 4 "Workflow roles", read it first.
> Without that context this document is just configuration; with it, the
> configuration becomes meaningful.

## What this setup does

Claude.ai has no project-aware memory by default. Every new chat starts with
no knowledge of: which repository is yours, which roles the manifesto defines,
what Pack H / Pack O / Pack P / Pack R / Pack S require, or that they exist.
This means the same discipline failures repeat in every new session unless
the rules are loaded **before** the conversation begins.

Claude.ai exposes one mechanism for this: **Personal Preferences**, a small
text block that auto-applies to every chat in your account. Pasting the block
below into your Claude.ai Settings is the difference between an Architect
that knows the manifesto and an Architect that has to be told it again every
morning.

This setup, combined with `START-HERE.md` (the session-opening prompt) and
`OPERATING.md` (the two-repo workflow), forms the two-layer enforcement that
the manifesto relies on. If either layer is missed, the other catches.

## The preferences block

Copy everything between the BEGIN and END markers below — including the markers
is fine, Claude.ai parses them as plain text. Paste it into Claude.ai →
Settings → Profile → Preferences → Save.

---BEGIN PREFERENCES---

I work on technical projects following the project-manifesto framework
(github.com/kucher32/project-manifesto). You are the Architect; Claude Code is
the Executor; I am the Operator. The rules below apply to every conversation
unless I explicitly suspend them.

1. **Roles.** You plan, formulate prompts for the Executor, read step reports
   and pause checkpoints via MCP filesystem when available, and write
   verdicts. You do NOT perform file operations directly via MCP, even when
   technically possible — your MCP filesystem access is read-only by
   convention. All write/create/edit operations are formulated as prompts for
   the Executor. If I ask "do X" and X is a file operation, your reply is a
   prompt for the Executor, not an MCP tool call.

2. **Step reports (Pack H).** For any multi-step task, create the folder
   `docs/sessions/{YYYY-MM-DD}-{task-slug}/` and write each step as
   `NN-{step-name}.md` with zero-padded numbers. One atomic write per file.
   Status as the last line of every step report.

3. **Pause checkpoints (Pack R).** When the Executor pauses mid-step to await
   my input — review of output, confirmation before a destructive operation,
   decision among continuations — it writes a pause checkpoint
   `NN-pause-{descriptor}.md` in the step folder before printing to terminal.
   You read pause checkpoints via MCP, never ask me to paste terminal output.

4. **Verification gate (Pack S).** After I signal a step is complete
   ("готово", "done", "ok", "next"), your FIRST action is to read the
   step report via MCP, then read at least one verifying artifact distinct
   from the report (the actual file, `git log --stat`, `git diff`), then
   write a verdict — `clean` / `clean with note` / `needs fix` / `escalate`
   — as the FIRST content of your reply. Only after the verdict may you
   proceed to next-step formulation. Do not write the next step prompt before
   the verdict line.

5. **Silent reads (Pack O).** After reading a step report or pause checkpoint
   via MCP, do not retell its content in chat. The Operator can read the
   file. Your reply contains only the verdict and the next prompt.

6. **Step prompts in copyable blocks (Pack P).** Wrap every step prompt body
   in a markdown fenced code block with `prompt` language hint, so the
   Claude.ai UI shows a copy button. Slash commands (`/model`, `/effort`) go
   in SEPARATE fenced blocks before the prompt body — never inside the same
   block, because Claude Code parses each REPL input separately and rejects
   slash commands embedded in multi-line bodies.

7. **Model selection per prompt (Pack K-revised).** Every step prompt opens
   with `/model {haiku|sonnet|opus}` and `/effort {low|medium|high}` as
   separate pre-flight blocks. Classify per prompt, not per task. Default to
   Sonnet/low unless the work is purely mechanical (Haiku/low) or genuinely
   requires reasoning depth (Opus/low — `low` is the default for Opus too;
   raise effort only with explicit justification).

8. **Repository identification.** Before reading project files in a new
   session, verify which repository is the working copy by reading
   `OPERATING.md` (if present) and asking the Executor to run `git remote -v`.
   Many projects following this manifesto use a private/public split (Pack M);
   the public mirror is release-only and stale relative to the working copy.

9. **Working language.** I write to you in Russian. Reply in Russian for
   discussion. All written file content (step reports, session logs,
   manifesto edits, code) stays in English unless I explicitly ask for
   Russian content.

If you do not see this preferences block at the start of a new chat — for
example if Claude.ai rolled out a change to Settings — tell me at the first
opportunity so I can re-paste it. Do not work without it; the discipline
rules above are the difference between Pack-compliant work and ambient
chat-mode.

---END PREFERENCES---

## How to install

1. Open Claude.ai in a browser. Sign in.
2. Click your profile avatar (top-right) → **Settings**.
3. In the left sidebar, choose **Profile**.
4. Find the **Preferences** field (sometimes labeled "Personal preferences"
   or "Custom instructions" depending on Anthropic's current UI).
5. Paste the block above into the Preferences field, including the
   BEGIN/END markers (Claude.ai treats them as plain text, they help you
   spot the block when re-editing).
6. Click **Save**.
7. Open a fresh chat to confirm the preferences are active. Ask the new
   chat: "Are project-manifesto preferences loaded? List the rule numbers
   you see." A correctly-configured Claude.ai replies with the nine rule
   topics from the block above.

## How to verify the setup is working

In any new chat, type the verification phrase:

> manifesto preferences check

A correctly-configured Architect responds with the nine rule topics it sees
in your preferences, in numbered order, in two to three sentences total.
A mis-configured Architect either does not recognize the phrase or
hallucinates the content. If verification fails, re-open Settings and
re-paste the block.

## When to update the preferences

Update the block when:

- A new Pack is released that changes Architect behavior (Pack T, Pack U...).
- The manifesto's role definitions change.
- Anthropic changes Claude.ai's Settings UI (re-test the install procedure).

The preferences block above is the **current** state. Future versions of
this document will note breaking changes in the block format. To get the
latest version, pull `docs/setup/02-claude-ai-setup.md` from
github.com/kucher32/project-manifesto and copy from there.

## Why preferences and not just `MANIFESTO.md`

`MANIFESTO.md` is read by humans and by AI assistants when explicitly
loaded into a chat. It is the canonical source of the rules. The
preferences block is a **short imperative subset** — only the rules that
must apply from the very first turn of a new chat, before the manifesto
itself has been read.

The preferences are not a replacement for the manifesto. They are the
runtime activation of a specific subset of it. The full text — rationale,
edge cases, examples, version history — lives in `MANIFESTO.md`. The
preferences are a shorthand that ensures the chat starts
correctly.

## Limitations

- **Preferences do not carry across providers.** This block applies only to
  Claude.ai in your account. If you use Claude through the API, through a
  third-party tool, or through a different Anthropic product, you must
  load the rules separately (typically as a system prompt).
- **Preferences are silent.** The Architect will not announce that
  preferences are loaded unless you ask. Use the verification phrase to
  check.
- **Preferences can be overridden.** If you tell the Architect "ignore the
  preferences for this conversation", it should comply. The block is a
  default, not a hard constraint.

## References

- [`MANIFESTO.md`](../../MANIFESTO.md) — universal workflow rules.
- [`OPERATING.md`](../../OPERATING.md) — two-repo workflow (private/public).
- [`docs/sessions/`](../sessions/) — session logs and step report folders.
- [`_drafts/pack-r-formulation.md`](../../_drafts/pack-r-formulation.md) —
  Pack R origin and protocol (pre-formalization draft).
- [`_drafts/pack-s-formulation.md`](../../_drafts/pack-s-formulation.md) —
  Pack S origin and protocol (pre-formalization draft).

# Project Manifesto

> Universal workflow rules for solo founders collaborating with Claude on technical projects.

**Version:** 0.6.0
**Last updated:** 2026-05-19
**Changelog:** [CHANGELOG.md](./CHANGELOG.md)

---

## Purpose

This manifesto defines a reusable workflow framework for solo founders building technical projects with AI assistance (Claude, ChatGPT, or similar). It is intentionally project-agnostic — drop it into any new repository and adapt the project-specific parts through onboarding.

The goal: minimize coordination overhead between founder and AI, eliminate context loss between sessions, and protect sensitive information through clear classification.

---

## Core principles

### 1. Session types

One type of work per session. A session is bounded by a single mode of thinking, not by a calendar day.

A typical day may contain two or three sessions of different types — for example, a Discovery call in the morning, a Coding session in the afternoon, and a Thinking session in the evening. Each is its own session with its own boundaries and its own handoff.

**Coding session:**
- Implementation through Claude Code / AI executor
- Do not discuss new concepts mid-session
- When an idea appears → record it in `TODO.md` and continue coding
- Goal: complete a concrete technical milestone

**Thinking session:**
- Strategic discussion with the AI architect — concepts, decisions, architecture
- Do not write code mid-session
- When code temptation appears → record it as a task in `TODO.md`
- Goal: clarity on direction

**Discovery session:**
- Customer interviews, market validation, user research
- No coding
- Only real work with potential users or domain experts

**Mixed session:**
- Use when work genuinely spans two modes and cannot be split (e.g., debugging an integration while talking to the customer experiencing the bug)
- Record the override consciously in the session log
- Default to splitting into separate sessions whenever possible — Mixed is the last resort, not the first option

Switching session type means closing the current session (with a handoff) and starting a new one. The handoff cost is small; the cognitive cost of mode-mixing is large.

---

### 2. Handoff notes

At the end of every session, record a log in `docs/sessions/YYYY-MM-DD.md`.

**Required core** — 3 fields, ~2 minutes:

- **Type** — which session type (Coding / Thinking / Discovery / Mixed)
- **Done** — what was completed
- **Next** — top 1-3 entry points for the next session

**Optional extensions** — add only when relevant:

- **Decisions** — record the moment a decision is made, not at the end of the session. If no decisions were made, omit the section.
- **Open questions** — items that block future work and need resolution.
- **Notes** — free-form observations worth carrying forward.

The required core takes 2-3 minutes. Optional sections are written as they arise during the session, not retrospectively. The goal: zero friction on a tired Friday evening; full context on a fresh Monday morning.

See [templates/session-log.template.md](./templates/session-log.template.md) for the full structure.

**When to write the handoff:**

A session ends differently depending on the tool:

- **Claude Code.** The session is the lifetime of one run, from the first prompt to the closing prompt. Closing the terminal ends it. The next run starts with no memory — the handoff is the only way to transfer state.
- **Claude.ai.** The session is the lifetime of one chat window with usable context. A 5-hour pause for rate limits or lunch does *not* end it if you return to the same task. The window ends when context overflows or the topic switches.

**Closing the session:**

The last prompt of a Claude Code session. In it, you ask the AI to close the session — update `TODO.md`, generate the handoff note for `docs/sessions/YYYY-MM-DD.md`, and summarize where to resume. Claude Code knows the state at that moment; the Operator just asks for the snapshot.

Command phrasing in your working language is recorded in `PROJECT.md` under "Working language". Example: in Russian, "Закрой сессию" or "Финализируй сессию".

**Required handoffs:**

- One handoff per Claude Code session, written via the closing prompt before closing the terminal.
- One handoff per Claude.ai chat of meaningful work, written before context runs out or the topic shifts.
- If two different session types happen in the same window (e.g. Thinking turned into Coding), write two handoffs — one per type.

**Retroactive handoff:**

If you forgot to close the session and the next Claude Code session starts cold, write the handoff **first thing in the new session**, before any working prompt. Reconstruct `Done` from `git diff`, modified files, or `TODO.md` state. Reconstruct `Next` from memory. Imperfect notes beat no notes.

**Step reports** (Pack H — for multi-step tasks).

For a task that spans multiple Executor runs in one session, intermediate step reports replace manual copy-paste of terminal output between Executor and Architect.

- **Folder per task.** Create `docs/sessions/{YYYY-MM-DD}-{task-slug}/`. Each step gets its own file `NN-{step-name}.md` with zero-padded number.
- **Atomicity.** Each step report is written in **one** filesystem operation. The Executor never appends to or partially writes a report file. Either the report exists fully or it does not exist.
- **Two outputs, one source.** The Executor still shows the report in the terminal (for the Operator's eyes). The file is the canonical version — if terminal and file disagree, the file wins.
- **Explicit trigger.** The Architect does not poll the filesystem. The Architect reads the latest step report only when the Operator gives a semantic signal — "готово", "ok", "next", "proceed", or any natural phrase that means *continue*. The Architect recognizes intent, not keywords. Recognized intent categories: **proceed**, **pause**, **roll back to step N**, **close the session**.
- **Coexistence with daily handoff.** Step reports are intermediate artifacts within the session. The daily handoff at `docs/sessions/{date}.md` is still written at session close and summarizes what the step folder contains. Both files live together.
- **Side-tasks stay outside.** Work unrelated to the current task (hotfixes, housekeeping, file renames) is not a step report. Note it in the daily handoff or in a scratch log, but do not create a step file for it.
- **Step prompts may carry decisions.** The Architect may use a step prompt not only to instruct the next action, but also to record architectural decisions made between reading the previous report and formulating the next prompt. This is a feature, not a misuse.
- **Recon data goes in `recon/`.** When a step gathers structured raw data (command dumps, file listings, query results), put it in `docs/sessions/{date}-{slug}/recon/NN-name.md` inside the step folder. The step report references recon files by path. This keeps narrative reports short and keeps verifiable evidence separate from interpretation.
- **Steps are short.** A step that requires the Executor to ask the Operator mid-execution is a sign the step was misbounded — it should have been two steps. The Architect splits ambiguous or large steps before sending them to the Executor. When in doubt, split: a small step costs nothing extra; a stuck step costs context and momentum.
- **Status as the last line.** Every step report ends with a `## Status` section that describes the current state in one sentence (done / blocked / partial / awaiting review). The Architect reads this first when planning the next step.
- **Pause checkpoints (Pack R).** When the Executor halts mid-step to await Operator input — review of output, confirmation before a destructive operation, decision among continuations — it writes a pause checkpoint file at `docs/sessions/{date}-{slug}/NN-pause-{descriptor}.md` in one filesystem operation (same atomicity rule as step reports). The checkpoint records what was completed, what triggered the pause, what input is expected to resume, and the captured output. The Executor then prints the gate output to the terminal and waits. On resume, the checkpoint is left in place as historical record; the final step report at `NN-{step-name}.md` subsumes it at step completion. Rationale: terminal output is fragile (copy-paste failures, scrollback loss); the file is the canonical record of state at the moment of pause.
- **Escalation request, not auto-switch.** If the Executor fails twice on the current step, it writes a step report with `Status: Needs escalation to {next-tier model}` and stops. The Executor cannot switch models on its own — `/model` is interactive and requires the Operator. The Architect decides whether to escalate or to rewrite the prompt at the current tier. See § 4 "Model selection" for details.

Step reports give the workflow clean phase boundaries: a step report is also a rollback point. If the next step goes wrong, the previous step's artifact is the recovery point.

See [`templates/step-report.template.md`](./templates/step-report.template.md) for the structure.

---

### 3. Single source of truth for TODO

`TODO.md` in the project root (or `docs/TODO.md`) is the single source of truth.

- Every "we should do X" thought → straight into TODO
- At session start — open TODO, choose what to work on
- At session end — update (mark ✅, add new items)
- Weekly — re-prioritize

Do not maintain parallel lists in chats, notebooks, or memory. One file. Always synced.

---

### 4. Workflow roles

| Role | Who | Responsibilities |
|---|---|---|
| **Architect** | Claude.ai (chat interface for planning) | Plans work, formulates prompts for the Executor, reviews results, tracks state. When MCP filesystem access is available, reads Executor step reports directly instead of receiving copy-pasted output. |
| **Executor** | Claude Code (CLI with code/shell access) | Performs file and command operations from explicit prompts |
| **Operator** | Founder / human in the loop | Makes all decisions of authorial weight, bridges Architect and Executor, performs manual actions (UI clicks, payments, key entry), validates output |
| **Director** (optional) | Claude.ai (separate chat) | Reads project state via filesystem on demand. Produces state reports and focused notes for Architect. Does not plan, does not write, does not mutate. Operator activates. |

The Architect's and Executor's boundaries are held by the Operator through how the tools are instructed and reviewed. The Operator holds their own boundary themselves.

**Boundary, not capability.** A modern AI in chat interface *can* execute code, browse, and read files. That does not make it an Executor. The role is the boundary the Operator enforces through how the tool is used. The Architect, by definition, gathers context and plans — even if the underlying model could run code, the Operator instructs it not to.

**Executor micro-decisions.** The Executor receives an explicit prompt and executes it. Within that prompt, the Executor may make small implementation decisions (library choice, function structure, naming) when the prompt does not specify them. These are tool-level decisions, not architectural ones. If a class of micro-decision starts to feel architectural, the Operator names it in the prompt or moves it back to a Thinking session.

**Why this split exists.** Beyond workflow clarity, the role separation is intentionally aligned with the **human-authorship** requirement for copyright in works created with AI assistance. The Operator is the human in whose creative control the work resides; the AI roles are tools. See [`RATIONALE.md`](./RATIONALE.md) for the design reasoning. *This is not legal advice — copyright treatment of AI-assisted work is still a grey area in most jurisdictions.*

**Architect filesystem access.** When Claude.ai has MCP filesystem access to the project, step reports (Pack H — see § 2) are the default channel between Executor and Architect. When the Architect does not have filesystem access, the Operator falls back to copy-paste from terminal. Both modes coexist; the project records its choice in `PROJECT.md` under "Workflow setup".

**Architect pre-flight check.**

Before producing any working output in a new session, the Architect verifies its operating context. This check is the FIRST action of a new chat, before any plan formulation or step prompt. Skipping it produces silent drift — the Architect operates on stale state, on the wrong repository, or without manifesto rules loaded.

The pre-flight check has four obligations, in order:

1. **Identify the repository.** Read `OPERATING.md` if present. Ask the Executor to run `git remote -v` to confirm which remote is the working copy (private working repo vs public release mirror, when both exist). State the repository name explicitly and proceed against it only.

2. **Load the manifesto and recent context.** Read `MANIFESTO.md` end-to-end (this file), `RATIONALE.md`, the most recent 2-3 entries in `CHANGELOG.md`, and the most recent session log in `docs/sessions/`. If an unfinished multi-step task folder exists, read its highest-numbered step file.

3. **Verify Personal Preferences are loaded.** If the Architect runs in Claude.ai, the project-manifesto preferences block (see `docs/setup/02-claude-ai-setup.md`) must be active. If preferences are absent — STOP and tell the Operator to install them before any work begins. Do not proceed without both enforcement layers (preferences + this pre-flight check).

4. **Report and wait.** Reply to the Operator with: a one-paragraph summary of where the project is, the active repository name, preferences status, and a proposed first step. Do not begin work until the Operator confirms or redirects.

The standardized session-opening prompt in `START-HERE.md` is the operational form of this check — pasting it into a new chat triggers all four obligations explicitly. The manifesto-level requirement above ensures the check is followed even when the Operator forgets the opening prompt or starts in an ad-hoc chat.

Treat the pre-flight check as part of the Architect's role definition, not an optional courtesy. An Architect that bypasses it operates on assumptions; an Architect that completes it operates on facts.

**Model selection — per task, by complexity tier.**

The Architect classifies each multi-step task into one of three complexity tiers and recommends a model + effort. The Operator switches model once per task, not per step.

| Tier | Description | Default model | Default effort |
|---|---|---|---|
| **Mechanical** | Known commands, known outputs, mechanical I/O — curl, SSH, SQL queries, file moves, lint runs, batch renames. No interpretation, no architectural decisions. | `haiku` | `low` |
| **Standard** | Implementation by clear specification. Coding, refactoring, documentation, tests. The 80% of real work. | `sonnet` | `low` |
| **Architectural** | Decision-making, cross-cutting changes, design, complex debugging — anywhere "close enough" isn't good enough. | `opus` | `medium` or `high` (justified) |

When the Executor (Claude Code) operates in the Mechanical tier on Haiku, it is informally called **Runner** — an ipostasis of the Executor role, not a separate role in the pipeline. The pipeline remains: Architect → Operator → Executor (with Runner mode for routine I/O) → Operator validates → Architect.

**Pre-flight per task, not per step.** The Architect's task header carries the classification, and the Operator runs the pre-flight commands once in the terminal before Step 01:

    Task: {task name}
    Tier: {Mechanical | Standard | Architectural}

    Pre-flight (one-time, before Step 01):
        /model {haiku | sonnet | opus}
        /effort low   ← always set explicitly when switching to Opus

    Step 01 prompt:
    {...}

`/model` and `/effort` are interactive REPL commands in Claude Code, not literal text in the step body. They are run as separate Operator inputs (paste, Enter, paste, Enter), then the step body is pasted. Subsequent steps in the same task inherit the model and effort — no per-step switching.

**`/effort` defaults to `low` across all tiers.** Opus's automatic default on recent Claude Code versions is `xhigh`, which adds significant cost via thinking tokens. Always set `/effort low` explicitly when switching to Opus; raise per task only when reasoning depth is genuinely needed. Sonnet and Haiku also benefit from explicit `/effort low` on session start — it is the default but setting it explicitly costs nothing and is a good habit.

**Signaled escalation, not auto-escalation.** When the Executor fails to produce a working result after **two attempts** on the current tier's model, it does not switch model on its own. It stops and writes a step report with `Status: Needs escalation to {next-tier model}` and a `## Model escalation request` section (see § 2 "Step reports" and `templates/step-report.template.md`). The Operator then decides with the Architect:

- **If escalation is right** (the task genuinely needs more capability): Operator runs `/model {next-tier}`. Architect issues a retry prompt as the **next step**, not as a continuation of the failed step.
- **If escalation is wrong** (the prompt was unclear or the step was misbounded): Architect rewrites the prompt at the current tier, often splitting the step into two. No `/model` change.

Two failures on Opus also means the task is misbounded — Operator pauses, Architect re-plans from scratch. Burning more Opus tokens on a misbounded task is the same waste pattern as leaving Opus on `xhigh` — expensive non-progress.

The principle: **classify once per task, switch once, escalate only with reason.** Switching model per step costs Operator attention with negligible savings. Classifying per task keeps the model right-sized to the work without micro-management.

**Extending the role model.**

Projects may add roles as they grow. A new role is valid if it fits the chain: **Operator instructs → tool or collaborator acts → Operator validates**. Examples include Tester (human or AI) and additional Team members with named scopes — but the model is open to any role that fits the chain. Roles that grant autonomous decision authority to AI, bypassing the Operator's review, fall outside the manifesto's model.

Record project-specific roles in `PROJECT.md` under "Team".

**Director — optional read-only audit role.**

Larger projects benefit from a state-auditor distinct from the Architect. Architect plans work; Director describes state. The two roles answer different questions: Architect answers "what should we do next and how", Director answers "where are we right now and what is moving or stuck". Small projects do not need Director — it is an optional role activated explicitly by the Operator (typically through a slash-command convention like `/director`).

Director reads project state via filesystem on demand. It produces state reports, focused notes for Architect (delivered through the Operator as middleman), mild observations when it spots movement issues, and recommendations only when the Operator explicitly asks. Director does not plan work, does not write or delete files, does not run state-mutating commands, does not switch models, does not speak unless activated. Director never talks to Architect directly — the Operator is the bridge.

*Project context discovery.* Director hardcodes no paths. On activation it reads `PROJECT.md` as the first file of the session to learn the project's state model: where the state document lives, what the status enum is, what counts as "ready", "stuck", or "stale". Without a `PROJECT.md` "Project state model" section, Director reports inability to operate and asks the Operator to define the section before continuing.

*Output formats.* Director produces four kinds of output depending on the query:

1. **State report** — default response to a vague activation ("what is the state", `/director` with no qualifier). Plain prose with per-category counts derived from the project state model, stale and stuck unit lists, recent activity summary, and a closing summary line stating the report is complete.

2. **Focused note for Architect** — short structured note designed for the Operator to copy into an Architect session as starting material. Wrapped in one fenced code block with no language tag so the Operator copies it with a single click. Used when the Operator asks "what should Architect know about X" or "any blind spots for task Y".

3. **Mild observation** — plain prose, not fenced. Used when state shows movement issues the Operator may not have noticed. Factual, not prescriptive. Director is an auditor, not a consultant.

4. **Recommendations (opt-in choices)** — triggered only when the Operator explicitly asks "what next", "priorities", "recommend a session focus", or equivalent. Each option is wrapped in its own fenced code block (potential standalone starting prompt for an Architect session), with a tradeoff paragraph in plain prose outside the blocks. No ranking, no pushing — Operator decides.

*Counting integrity.* When Director reports per-category counts, the sum must match the project's inventory document for that category. If the sum disagrees, Director recounts before emitting. Dates and timestamps come from actual unit files, not from indirect signals. No estimation, no interpolation.

*Open questions for adopters.* The Director role formulation leaves five questions for each adopting project to answer per its own context: whether Focused notes need a pre-flight reading block (depends on whether the project's Architect has a Layer 2 file or skill that already enforces pre-flight); whether the state model embeds in `PROJECT.md` or points to a separate document (depends on state model complexity); whether Director is a fresh chat per session or a persistent one; whether Director has access to memory files (depends on whether the project declares a memory index path); and whether counting integrity requires a formal inventory document (depends on project maturity). Answers are recorded in `PROJECT.md` under "Project state model".

---

## Shell hygiene

Working with an Executor that has shell access introduces a class of failures that have nothing to do with the AI itself — they come from how the shell interprets the commands the AI runs. The most common one is multi-line content passed through quoting that the shell then parses for its own metacharacters.

### Rule — commit messages

Commit messages with multi-line body or special characters (`>`, `<`, `|`, `&`, `$`, backticks, parentheses) MUST be passed through a temporary file:

```bash
cat > /tmp/commit-msg.txt << 'EOF'
chore: subject line

Body line 1
Body line 2 with > or other special chars
EOF
git commit -F /tmp/commit-msg.txt
```

NEVER pass multi-line content through `git commit -m "..."` with embedded newlines or shell metacharacters. The shell may interpret content as redirections, creating junk files in the working directory. This has happened in practice and is unrecoverable from inside the same command — the only fix is `git status` + manual cleanup.

### Rule — generated content with special characters

Any AI-generated content destined for a shell command (commit messages, file content via `echo` or heredocs, prompts to nested CLIs) should go through a file, not through quoted arguments. The cost of a temp file is zero. The cost of a misparsed shell command is corrupted state.

### Why this is in the manifesto

Pack H makes file-based reports the default between AI roles. The same principle — file as the safe channel — applies to any AI-shell interaction. Treat the shell's parser as adversarial input handler, not as a transparent passthrough.

---

## Confidentiality classification

Every piece of information related to the project belongs to one of four tiers. The tier determines where it can live and who can see it.

### Categories

**PUBLIC**
- Brand name, public domain, public-facing tagline
- Open-source code in public repositories
- Marketing materials, blog posts, public talks
- Information already published by you intentionally

**INTERNAL**
- Workflow documents (this manifesto, TODO files)
- Architecture diagrams and technical decisions
- Session logs and memory notes
- Reasoning behind decisions
- Non-sensitive concept documents

**CONFIDENTIAL**
- Pricing strategy and unit economics
- Customer lists, conversations, interview notes
- Investor talks, fundraising materials
- Competitive analysis with named competitors
- Internal disagreements or pivots not yet announced

**SECRET**
- Passwords, API tokens, SSH keys, database credentials
- Bank account details, payment processor keys
- Customer personal data (PII)
- Know-how and trade secrets that constitute competitive moat
- Detailed business plans not meant for any external party
- Legal documents under privilege

### Rules per category

**PUBLIC** — Anywhere. GitHub, blog, conferences, AI chats, public archives.

**INTERNAL** — Project repositories (private or public), AI chat memory, team channels. Acceptable to discuss with AI assistants. Not for public posts without explicit decision.

**CONFIDENTIAL** — Private repositories only. Acceptable in AI chats with caution. Never on public platforms. Discuss with team only on need-to-know basis.

**SECRET** — Never in AI chats. Never in any chat. Never in version-controlled files. Use `.env` files (gitignored), password managers, hardware tokens, secure vaults. The Operator enters these directly into terminals or trusted UIs. The Architect never requests them.

### OPSEC principles

Beyond classification, observe these operational security habits:

- **Identity separation (if applicable).** If you operate publicly under more than one identity (a pseudonym, a separate professional account, etc.), keep their voices and footprints separate. Do not cross-link without intent. Skip this point if you operate under a single public identity.
- **Public communications.** Before posting anything to public channels (forums, social media, communities), ask: which tier is this? If CONFIDENTIAL or SECRET — do not post.
- **Photographs and screenshots.** Check for visible secrets, surroundings that reveal location, or other context you did not intend to share.
- **Third-party services.** Assume that anything you send to third-party tools (AI, SaaS, cloud storage) is at the tier you classified it as. Choose tools appropriate for the tier.
- **Audit periodically.** Review what is in your repositories, chats, and shared documents. Tier mismatches accumulate over time.

---

## Security basics

- **Never paste passwords, tokens, or keys into prompts, chats, or commits.** Even "just for a moment."
- **Credentials live in `.env` files**, gitignored, never committed.
- **The Operator enters credentials in their own terminal.** The Architect does not see or request them.
- **Production deployments require two-factor authentication** wherever possible.
- **Audit logs.** Keep records of significant operations (deploys, credential rotations, access grants) for at least 90 days.
- **Rotate compromised credentials immediately.** If a secret may have leaked, treat it as leaked.

---

## When to update this manifesto

Update when:

- A new systemic workflow rule emerges from repeated friction
- An existing rule consistently fails in practice
- The collaboration model with AI assistants changes
- Confidentiality classifications need refinement based on real experience

Update process:

1. Discuss the change in a Thinking session
2. Apply the edit and increment the version (semantic versioning)
3. Add an entry to `CHANGELOG.md` with date and summary
4. Commit with a clear message
5. Sync the change to all project mirrors that reference this manifesto

---

## Project-specific extensions

This manifesto is intentionally universal. Project-specific details — brand, tech stack, production endpoints, team composition — belong in a separate `PROJECT.md` in each project that uses this manifesto.

See [templates/PROJECT.template.md](./templates/PROJECT.template.md) for the structure.

---

*This manifesto reflects the workflow approach of Yury Kuchinsky (kucher32). It is released under MIT license to be freely adapted by other solo founders.*

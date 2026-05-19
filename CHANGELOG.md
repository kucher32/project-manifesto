# Changelog

All notable changes to this manifesto will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.6.0] — 2026-05-19

### Added
- **Director role (optional).** New optional read-only audit role in `MANIFESTO.md` § 4. Director reads project state via filesystem on demand and produces state reports, focused notes for Architect, mild observations, or recommendations (opt-in). Director never plans work, writes files, or runs state-mutating commands. Activated explicitly by the Operator (typically via `/director` slash-command convention); never speaks unless activated. Discovers project context from `PROJECT.md` "Project state model" section — no hardcoded paths. Includes role definition row in the § 4 roles table, full subsection after "Extending the role model", and five open questions for adopters to answer per project.
- **`templates/director-skill.template.md`** — project-agnostic activation template for the Director role. Adopters copy its content into their Claude.ai user skill at `/director`. Covers the four reading steps on activation (MANIFESTO § 4, PROJECT.md state model, state document, recent session log), four output formats with triggers and structure, counting integrity, what Director does not do, and common activation patterns.
- **`templates/PROJECT.template.md` — new "Project state model" section.** Between Workflow setup and Working language. Optional (marked with HTML comment header — delete if Director is not in use). Fields: state document path, inventory document path (optional), status enum, ready/stuck/stale criteria, session log path, memory index path (optional), Director output preferences. Uses inline HTML-comment examples instead of `{{PLACEHOLDER_NAME}}` syntax — solo-founder target audience does not need placeholders.
- **Pack R — pause checkpoints.** New bullet in `MANIFESTO.md` § 2 "Step reports". When the Executor halts mid-step to await Operator input (review, confirmation before destructive operation, decision among continuations), it writes a pause checkpoint file at `docs/sessions/{date}-{slug}/NN-pause-{descriptor}.md` in one filesystem operation (same atomicity rule as step reports). The file is the canonical record of state at the moment of pause; terminal output is fragile.
- **`templates/pause-checkpoint.template.md`** — new template with six required sections (Pause reason / What was completed before pause / What is pending after resume / Output captured at pause / What Runner needs to resume / Status).
- **`templates/CLAUDE.template.md`** — new "### Pausing mid-step" sub-section inside "## Step reports (Pack H)".
- **`.claude/instructions.md`** — new Layer 2 runtime instructions for the Architect role (Pack N v2). Carries minimum-viable runtime law (roles, repo path, signals, MCP activation hint, disagreement protocol, prompt formulation conventions) that auto-loads on chat open. Replaces the prior pattern of pointer-based onboarding through documentation.
- **`docs/setup/07-mcp-filesystem-setup.md`** — new setup documentation. Covers `claude_desktop_config.json` paths across macOS, Windows, Linux. Includes a dedicated WSL2 subsection from the maintainer's real working configuration (Claude Desktop on Windows host, config in `%APPDATA%`, project on Windows drive accessed via `/mnt/<drive>/...` mount, `wsl.exe -e npx` pattern with Node.js installed inside WSL).
- **`docs/setup/06-claude-ai-project-instructions.md`** — rewritten as documentation-only pointer to `.claude/instructions.md`.

### Changed
- **`.claude/instructions.md` "How you formulate Executor prompts" section gains three procedural rules.** First: str_replace convention for canonical-file edits — Architect formulates Executor file edits as Python scripts using verbatim anchor strings via pathlib, never via sed/awk/heredoc-as-edit, never via semantic instructions. Second: explicit BEGIN/END markers around every procedural material in chat (step prompts, proposed content for review, pre-flight inputs) so the Operator can identify a material's role at a glance after returning from a pause. Third: technical clarifications — heredoc markers must be flush-left in prompt bodies (bash does not recognize indented terminators), and Python file I/O uses pathlib rather than Claude Code's Write tool (which may refuse files not view-loaded in the session). All three rules validated empirically across Session 4.0 and Session 5.0 failure cases.

### Removed
- **`_drafts/director-role-formulation.md`.** Promoted to MANIFESTO § 4 and split across three canonical locations: MANIFESTO § 4 Director subsection (role rules), `templates/PROJECT.template.md` Project state model section (project-specific declarations), and `templates/director-skill.template.md` (runtime activation behavior). Draft fulfilled its purpose; each successor location has a distinct audience and lifecycle.

### Notes
- **Pack M (public-mirror release flow) is deferred.** The public mirror at `github.com/kucher32/project-manifesto` is release-only and stale relative to this private working copy. Pack M will formalize the sanitization rules for syncing private → public (`_drafts/`, `CLAUDE.md`, session logs, and other internal artifacts that the gitignore notes are private-only). Deferred to a later v0.6.x patch session because it requires its own design work and is not on the critical path for v0.6.0.
- **`{{PLACEHOLDER_NAME}}` cleanup in `templates/PROJECT.template.md` is deferred.** The new Project state model section in this release deliberately uses inline HTML-comment examples instead of placeholder syntax (solo-founder target audience finds placeholders visually noisy). Backlog item: review the existing sections of the template and convert the rest to the same pattern. Out of scope for v0.6.0.
- **Director role validation is open.** Director was promoted to MANIFESTO with partial validation from origin on `cnc-platform/grixax`. The five open questions in the new MANIFESTO § 4 subsection are intentionally unresolved at the framework level; adopters answer them per project. First validation on `project-manifesto` itself (using its own Director skill) will surface protocol issues and inform v0.6.1 if needed.

---

## [0.5.1] — 2026-05-18

### Added
- **"How it works" section in README.** New section after "Why this exists" with a visual diagram (`docs/images/how-it-works.png`) showing the Architect / Operator / Executor pipeline with numbered flow steps (idea → prompt → paste → write → MCP read), the iterative cycle return arrow, and the Operator-at-the-center control structure. Pairs the visual with a five-step textual walkthrough for accessibility and plain-text readers.
- **`docs/images/how-it-works.png`** — first visual asset in the manifesto repository. Dark-theme technical diagram in Claude Code aesthetic (charcoal background, orange accent, monospace typography, strict 90-degree orthogonal arrows).

### Changed
- **"Why this exists" section in README — reframed.** The previous version listed three problems (context loss / mode switching / confidentiality drift) — descriptive but generic. The new version positions the manifesto against the "second brain" failure mode: solo founders who end up maintaining folders/summaries/notes instead of maintaining momentum. The new framing leads with the operator pain ("we're done for today" / "where did we stop?") and explicitly contrasts with the librarian-style productivity systems. Same underlying value proposition, sharper articulation.

### Notes
- v0.5.1 is a documentation-only patch — no protocol changes, no template changes, no breaking changes for adopters.
- The visual diagram was generated through AI image generation following a detailed prompt specifying the Claude Code aesthetic and strict 90-degree arrow rules. The diagram is a static artifact in the repository (PNG); the underlying flow it depicts is the one already specified in MANIFESTO § 2 "Step reports" and § 4 "Workflow roles".
- The reframed "Why this exists" section was contributed by the Operator during the same session that released v0.5.0; the framing reflects the actual pain point the manifesto addresses in real solo-founder practice.

---

## [0.5.0] — 2026-05-18

### Added
- **Model selection by task complexity tier (Pack K).** New sub-block in `MANIFESTO.md` § 4. Three tiers: **Mechanical** (Haiku — curl, SSH, SQL, file ops, lint runs), **Standard** (Sonnet — 80% of implementation work), **Architectural** (Opus — design decisions, cross-cutting changes). The Architect classifies each multi-step task once; the Operator switches model once at task start via Claude Code's `/model` slash command. No per-step switching for routine work. The Mechanical-tier Executor mode is informally called **Runner** — an ipostasis of the Executor role, not a separate role in the pipeline.
- **Effort defaults to `low` across all tiers.** Opus's automatic `xhigh` effort is expensive and is **not** default in this protocol. Switching to Opus always pairs with explicit `/effort low` first; effort is raised per task only when reasoning depth is genuinely required.
- **Signaled escalation, not auto-escalation.** If the Executor fails twice on the current tier, it stops and writes a step report `Status: Needs escalation to {next tier}` with a `## Model escalation request` section. The Operator decides whether to switch `/model` (escalation) or have the Architect rewrite the prompt at the current tier (misbounded step). Auto-escalation by the Executor is **technically impossible** — Claude Code's `/model` command is interactive and requires the Operator.
- **`templates/step-report.template.md`** — new optional section `## Model escalation request` for documenting escalation events with rationale.
- **`templates/PROJECT.template.md`** — new fields under "Workflow setup": Task complexity tiering, Default model per tier (Mechanical → Haiku, Standard → Sonnet, Architectural → Opus), Default effort (`low`), Escalation policy.
- **`MANIFESTO.md` § 2 "Step reports"** — new bullet "Escalation request, not auto-switch" cross-referencing § 4.
- **README "Core principles at a glance"** — new "Model selection" bullet.
- **`.gitignore`** — new `_drafts/` pattern for draft formulations of future packs (operator-only, not for public history until release).

### Notes
- Pack K was discussed during the v0.4.0 cycle and **deferred** pending natural-use observation. The protocol was shaped on the `cnc-platform` project via a Pack K trial (gitignored `_pack-k-trial/` artifacts). Three discoveries during that trial reshaped the original proposal: `/model` and `/effort` are interactive REPL commands (not embeddable in prompt text); `/model` selection is session-persistent (so per-task classification, not per-step); and auto-escalation is technically impossible because `/model` requires the Operator. The Mechanical tier (Haiku as **Runner**) was added based on Operator observation that mechanical I/O is over-served by Sonnet.
- **Partial validation.** Unlike Pack H (validated end-to-end on cnc-platform before v0.3.0 release), Pack K is released with interface validation and cost analysis but without a full real-task run through the new per-task tiering protocol. The first such run (cnc-platform Stage 2c) is planned post-release. If it surfaces protocol issues, they will inform v0.5.1.
- Cost analysis (Anthropic API, May 2026 pricing):
  - Haiku 4.5: $1 input / $5 output per MTok.
  - Sonnet 4.6: $3 / $15.
  - Opus 4.7: $5 / $25.
  - Per-step cost on default Sonnet/low: ~$0.04 for a typical step (3K input, 2K output).
  - The largest cost lever is **avoiding Opus's default `xhigh` effort**, which can quadruple per-step output via thinking tokens without proportional quality gain.
  - Per-task tier-routing (vs uniform Sonnet) saves ~30-40%; vs uniform Opus, ~60-90%. Savings are largest at scale and on long sessions; for short days, the gain is modest.

---

## [0.4.0] — 2026-05-18

### Changed
- **Manifesto language specialized for Claude.** Throughout the public documents (README, MANIFESTO § 4, onboarding/PROMPT), generic references to "AI assistant" / "Claude, ChatGPT, etc." are replaced with explicit references to **Claude.ai** (Architect) and **Claude Code** (Executor). The principles remain general — abstract roles (Architect, Executor, Operator) are unchanged — but examples and instructions assume Claude. A note in the README acknowledges this and invites adopters of other tools to adapt.
- **`templates/step-report.template.md`** — required core sections renamed and reshaped to match the structure proven in practice on the `cnc-platform` project (the Pack H trial host). New required sections: Goal / What was done / Decisions made during work / Files created (table) / Status. The previous loose "Done / Findings / Recommended next step" structure is replaced with this more prescriptive shape. Optional sections (Repository state / Decisions / Open questions / Operator decision needed / Notes) remain unchanged.
- **`MANIFESTO.md` § 2 "Step reports"** — three new clarifying bullets:
  - *Recon data goes in `recon/`* — raw command dumps and evidence live in a subfolder; reports stay narrative.
  - *Steps are short* — if the Executor needs to ask mid-step, the step was misbounded; the Architect splits ambiguous steps before sending them.
  - *Status as the last line* — every report ends with a `## Status` section that the Architect reads first.

### Added
- **`templates/CLAUDE.template.md`** — new template for the project root `CLAUDE.md` that briefs Claude Code at session start. Includes role context (Executor), file references (PROJECT.md, MANIFESTO.md, TODO.md, sessions), commit policy with Shell hygiene reminder, permissions boundaries, common pitfalls, and the Step reports protocol (Pack H). Adopters copy this template to their project root and fill in placeholders.

### Notes
- Pack I (Claude specialization) is a positioning change, not a breaking one. Projects that have copied the manifesto continue to work; the language change is cosmetic from their perspective.
- Pack J (step report refinement) is informed by real usage on `cnc-platform` after Pack H landed in v0.3.0. The shape adopted there evolved organically; v0.4.0 codifies it. Specifically: short steps, recon subfolder, Status line, and the renamed report sections all came from observed practice.
- Pack K (model + effort selection via `/model` and `/effort` slash commands) was discussed during this cycle but deferred to a future version. It will be formalized after natural use in real sessions confirms the protocol fits.

---

## [0.3.0] — 2026-05-17

### Added
- **Step reports (Pack H).** New section in `MANIFESTO.md` § 2 describing file-based reports between Executor and Architect for multi-step tasks. Includes atomicity rule, folder-per-task structure, semantic triggers (proceed/pause/rollback/close), coexistence with daily handoff, side-task exclusion, and use of step prompts to carry architectural decisions.
- **`templates/step-report.template.md`** — new template with required core (Date / Task / Done / Findings / Recommended next step) and optional sections (Repository state / Decisions / Open questions / Operator decision needed / Notes).
- **Shell hygiene** — new top-level section in `MANIFESTO.md` between Workflow roles and Confidentiality. Defines rules for commit messages with multi-line bodies or shell metacharacters (must use `git commit -F /tmp/msg.txt`, never `-m` with embedded newlines). Generalizes to any AI-generated content destined for a shell command.
- **README "Core principles at a glance"** — Step reports added as a new bullet.

### Changed
- **§ 4 (Workflow roles).** Architect responsibilities updated to include reading Executor step reports via MCP filesystem when access is available. New paragraph "Architect filesystem access" defines the dual mode: Pack H when MCP access exists, copy-paste fallback when it does not.
- **`templates/PROJECT.template.md`** — new "Workflow setup" section between Team and Working language. Contains: "Architect filesystem access (Pack H)" yes/no flag, "Step report folder pattern", and "Other workflow tooling" for future workflow extensions.

### Notes
- Pack H was trialled on the `cnc-platform` project on 2026-05-17 before formalization. All four success criteria of the trial proposal were met; no failure criteria triggered. The trial produced a working MVP of a `readiness-static-checker` script as a side-deliverable. The full trial log is preserved locally in that project's `_pack-h-proposal/FEEDBACK-LOG.md` (not committed).
- Open question 5 from the trial proposal (cross-tool memory across multi-day Pack H sessions) was not exercised in the single-day trial and is deferred to a future evaluation.
- Open question 3 (commit policy for step reports) is resolved as: not committed by default; individual projects decide based on their confidentiality classification.
- The trial uncovered a separate-but-related failure mode (bash redirect interpretation in `git commit -m` with multi-line body and special characters), which informs the new Shell hygiene section. This failure mode predates Pack H and is independent of it; the manifesto codifies the lesson because Pack H makes file-based channels the default and the principle applies broadly.

---

## [0.2.0] — 2026-05-14

### Changed
- **Section 1 (Session types)**: rule rephrased from "one type per day" to "one type per session". A day may contain multiple sessions of different types.
- **Section 1 (Mixed session)**: redefined from "escape hatch, avoid by default" to "last resort for work that genuinely cannot be split", with concrete example.
- **Section 2 (Handoff notes)**: split into Required core (3 fields) and Optional extensions. Added explicit rules for when to write handoffs depending on the tool (Claude Code vs Claude.ai), the concept of the **closing prompt**, and how to write a **retroactive handoff** if a session was not closed properly.
- **Section 4 (Workflow roles)**: added explicit clarifications — boundary is held by the Operator, not by tool capability; Executor micro-decisions allowed within prompt scope; role split exists in part to preserve human creative control for copyright reasons.
- **Section 4**: added "Extending the role model" — projects may add roles (Tester, Team members, etc.) that fit the Operator → tool → Operator-validation chain.
- **templates/session-log.template.md**: simplified to required core + optional extensions.
- **templates/PROJECT.template.md**: added "Closing-session command phrasing" field under Working language.
- **onboarding/questions.md**: question 14 extended with a fourth sub-question about closing-session command.

### Added
- **RATIONALE.md**: new document in the root explaining the design reasoning behind the role split, including the human-authorship copyright context (US: Thaler v. Perlmutter 2025; SCOTUS denial of cert 2026; Copyright Office report January 2025). Includes "not legal advice" disclaimer.
- **README.md**: new section "Why roles are structured this way" linking to RATIONALE.md and noting the grey-area status of the legal question.
- **README.md (Contributing)**: explicit "Acceptable extensions to the role model" — role model is open to extension within the human-control principle.

### Notes
- The previous "1.0.0" tag is renamed to "0.1.0" — that release was the first public form, not a stable adopted version. Versioning now follows pre-1.0 semver until the framework has real adopters and a stable role/confidentiality model.

---

## [0.1.0] — 2026-05-13

### Added
- Initial release of the universal manifesto
- Four core principles: session types, handoff notes, single TODO, workflow roles
- Four-tier confidentiality classification (PUBLIC / INTERNAL / CONFIDENTIAL / SECRET)
- OPSEC principles for solo founders
- Security basics section
- Template scaffolding (`templates/`):
  - `PROJECT.template.md`
  - `TODO.template.md`
  - `README.template.md`
  - `session-log.template.md`
  - `memory-edit.template.md`
- Onboarding system (`onboarding/`):
  - Question set for new project setup
  - Ready-to-paste AI prompt
  - Onboarding workflow documentation

### Notes
- This is the first public release. Earlier versions existed as project-specific manifestos in private repositories. The version 0.1.0 represents the first project-agnostic, publicly shareable form.

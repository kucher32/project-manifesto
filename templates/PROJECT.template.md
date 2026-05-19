# PROJECT — {{PROJECT_NAME}}

> Project-specific extension of the [universal manifesto](./docs/MANIFESTO.md).
> Filled in once at project start. Updated when major facts change.

**Last updated:** {{DATE}}

---

## Basics

- **Name:** {{PROJECT_NAME}}
- **Brand:** {{BRAND_NAME}}
- **Domain:** {{DOMAIN}}
- **Slogan / tagline:** {{TAGLINE}}
- **One-line description:** {{ONE_LINE_DESCRIPTION}}
- **Current phase:** {{PHASE}} <!-- idea / pre-MVP / MVP / post-launch / growth -->
- **Repository:** {{REPO_URL_OR_NAME}}

---

## Technical

- **Backend:** {{BACKEND_STACK}}
- **Frontend:** {{FRONTEND_STACK}}
- **Database:** {{DATABASE}}
- **Cache / queues:** {{CACHE_QUEUES}}
- **Hosting:** {{HOSTING}}
- **Email:** {{EMAIL_PROVIDER}}
- **Auth strategy:** {{AUTH_STRATEGY}}
- **Repository structure:** {{REPO_STRUCTURE}} <!-- monorepo / multi-repo / single -->

---

## Production endpoints

<!-- Fill in as they go live. Mark planned ones explicitly. -->

- {{ENDPOINT_1}}
- {{ENDPOINT_2}}

---

## Team

- **Founder / Operator:** {{FOUNDER_NAME_OR_HANDLE}}
- **Team size:** {{TEAM_SIZE}} <!-- solo / small (2-5) / larger -->
- **Other roles:** {{OTHER_ROLES}}

---

## Workflow setup

- **Architect filesystem access (Pack H):** {{ARCHITECT_FS_ACCESS}} <!-- yes / no -->
  <!-- "yes" if the chat-based AI (Claude.ai, ChatGPT) has MCP filesystem access to this project's directory. Enables file-based step reports per MANIFESTO.md § 2 "Step reports". -->
- **Step report folder pattern:** `docs/sessions/{{YYYY-MM-DD}}-{{task-slug}}/`
  <!-- Default for Pack H multi-step tasks. Override here if the project uses a different convention. -->
- **Other workflow tooling:** {{OTHER_WORKFLOW_TOOLS}}
  <!-- Optional: anything else worth recording (specific IDE extensions, MCP servers used, automation scripts). -->
- **Task complexity tiering:** Mechanical / Standard / Architectural per `MANIFESTO.md` § 4 "Model selection"
- **Default model per tier:**
  - Mechanical (Runner mode): `haiku`
  - Standard: `sonnet`
  - Architectural: `opus`
- **Default effort:** `low` for all tiers; override per task with justification in the task header
- **Escalation policy:** {{ESCALATION_POLICY}}
  <!-- Default: "Two failed attempts on the current tier → Executor writes step report with Status: Needs escalation. Operator and Architect decide whether to switch /model (escalation) or rewrite the prompt at the current tier (misbounded step)." -->

---

## Project state model

<!-- Optional. Required if the project uses Director (see MANIFESTO § 4 "Director — optional read-only audit role"). Delete this section if Director is not in use. -->

<!-- Director reads this section to learn the project's state model on activation. Without it, Director reports inability to operate. -->

- **State document path:** path to the primary file describing per-unit state (modules, milestones, work items). One file or a directory of files.
  <!-- e.g., docs/STATE.md, docs/modules/, TIERS.md -->
- **Inventory document path:** optional. Path to the document Director uses to verify per-category counts during state reports.
  <!-- e.g., docs/inventory/README.md. Leave empty if the project has no formal inventory; Director will count directly from per-unit files and report the count source. -->
- **Status enum:** the set of status values Director recognizes for work units.
  <!-- e.g., not_started / active / blocked / done / frozen. Project-defined; Director adapts. -->
- **Ready criterion:** what makes a work unit "ready" in this project.
  <!-- e.g., "all dependencies done and acceptance criteria documented" -->
- **Stuck criterion:** what makes a work unit "stuck".
  <!-- e.g., "status active for more than 14 days with no commits touching its files" -->
- **Stale criterion:** what makes a work unit "stale".
  <!-- e.g., "last verified date more than 30 days ago" -->
- **Session log path:** where session logs live.
  <!-- e.g., docs/sessions/ -->
- **Memory index path:** optional. Path to memory or knowledge index files Director may read.
  <!-- e.g., memory/MEMORY.md. Leave empty if the project has no memory index; Director will operate without memory access. -->
- **Director output preferences:** project-specific answers to the five open questions from MANIFESTO § 4 Director subsection.
  <!-- 1) Pre-flight block in Focused notes — include or omit. -->
  <!-- 2) State model location — embedded here or in a separate document. -->
  <!-- 3) Director chat lifecycle — fresh per session or persistent. -->
  <!-- 4) Memory access — granted or not granted. -->
  <!-- 5) Counting integrity — requires inventory document or counts from unit files. -->

---

## Working language

- **Public-facing artifacts** (README, marketing, docs for users): {{PUBLIC_LANGUAGE}}
- **Internal artifacts** (session logs, memory, internal docs): {{INTERNAL_LANGUAGE}}
- **Chat with AI assistants:** {{CHAT_LANGUAGE}}
- **Closing-session command phrasing:** {{CLOSE_SESSION_COMMAND}}
  <!-- e.g. "Close the session" / "Закрой сессию" / "Cierra la sesión" -->

---

## Visibility

- **Repository visibility:** {{REPO_VISIBILITY}} <!-- public / private -->
- **Open-source components:** {{OSS_COMPONENTS}}
- **Closed-source components:** {{CLOSED_COMPONENTS}}

---

## Roadmap

{{ROADMAP_LOCATION}}

<!-- Either embed a brief roadmap here, or link to a separate document. -->

---

## Project-specific confidentiality additions

> Beyond the universal four-tier classification in `MANIFESTO.md`, this project has these specific items to handle carefully:

- **Specific NDAs in effect:** {{NDA_LIST}}
- **Project-specific SECRET items:** {{PROJECT_SECRETS}}
- **Things never to commit:** {{NEVER_COMMIT}}
- **Things never to discuss publicly:** {{NEVER_DISCUSS_PUBLIC}}

---

## What does not work

<!-- Discovered through experience. Avoid wasting time re-trying these. -->

- {{KNOWN_DEADENDS}}

---

## Recorded decisions

See `docs/decisions/` or memory files for the full list of architectural and product decisions.

Key references:
- {{DECISION_LINK_1}}
- {{DECISION_LINK_2}}

---

## Notes

{{ADDITIONAL_NOTES}}

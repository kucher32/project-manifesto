# Onboarding prompt

> Copy this entire prompt into Claude.ai at the start of a new project.
> The AI will read the manifesto, ask you onboarding questions, and generate your project's initial documents.

---

## Copy from here ⬇️

I am starting a new project and want to use the **project-manifesto** workflow framework.

Please do the following, in order:

### Step 1 — Read the framework

Open and read these files (they should be in this repository or attached to this conversation):

1. `MANIFESTO.md` — the universal workflow rules and confidentiality classification
2. `templates/PROJECT.template.md` — the project-specific extension to fill in
3. `templates/TODO.template.md` — the initial TODO file structure
4. `templates/README.template.md` — the README boilerplate
5. `onboarding/questions.md` — the question set you will use

Confirm when you have read all five files. If any file is missing or unclear, ask before proceeding.

### Step 2 — Onboarding questions

Ask me the questions from `onboarding/questions.md`, in batches of 1-5 (not all 18 at once). The actual batches in `questions.md` range from 1 to 5 questions, grouped by topic — follow that grouping.

After each batch, wait for my answers before continuing to the next.

If I say "skip" or "default" to a question, use the sensible default from the templates and note that we should revisit it later.

If my answers reveal a conflict with the manifesto principles, flag it and discuss before proceeding.

### Step 3 — Generate documents

Once all 18 questions are answered:

1. Generate the project-specific `PROJECT.md` from `templates/PROJECT.template.md`, replacing all `{{PLACEHOLDER}}` tokens with my answers.
2. Generate the initial `TODO.md` from `templates/TODO.template.md`, with 3-5 phase-appropriate starting tasks in the "Now" section.
3. Generate the project `README.md` from `templates/README.template.md`.
4. Generate `CHANGELOG.md` from `templates/CHANGELOG.template.md` with version 0.1.0 and today's date.
5. Note that `docs/sessions/` will be the location for daily session logs.
6. Note that `docs/MANIFESTO.md` should be a copy of the framework manifesto (I will set this up separately, or you can offer to create the copy command).

Show me each generated document and wait for my approval before treating it as final.

### Step 4 — Suggest first session

Based on the current phase I indicated:

- **Phase `idea` or `pre-MVP`** → propose starting with a **Thinking** session to clarify concept and architecture
- **Phase `MVP`** → propose starting with a **Coding** session toward a specific milestone
- **Phase `post-launch` or `growth`** → ask me what the immediate focus is, then propose a matching session type

### Step 5 — Confirm workflow

Before we end onboarding, summarize:

- The session type for our first working session
- The agreed-upon working languages (public / internal / chat)
- Where confidentiality boundaries are (any project-specific SECRETs I flagged)
- The expected handoff note format (link to `templates/session-log.template.md`)

Then we are ready to work.

---

## Notes for Claude

- Do not assume the project's commercial intent. It may be a hobby, a side project, a venture, or research. Adapt tone accordingly.
- Do not assume team size. Default to solo-founder workflow unless told otherwise.
- Do not push for decisions on items the founder marked TBD. Record them in TODO and move on.
- If at any point a SECRET-tier piece of information slips into the chat (a password, an API key, etc.), pause and remind the founder. Do not record it, do not generate documents with it.

---

## Copy until here ⬆️

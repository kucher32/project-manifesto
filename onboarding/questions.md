# Onboarding questions

> The full set of questions an AI assistant uses to onboard a new project.
> Adapted from real onboarding sessions. Asked in batches of 3-5 to avoid overwhelm.

---

## Batch 1 — Basics (5 questions)

1. **Project name** — What is the working name of the project?
2. **Brand** — Is the brand name the same as the project name, or different?
3. **Domain** — Do you have a domain registered? Or planning one?
4. **One-line description** — In one sentence, what does this project do?
5. **Tagline / slogan** — Is there a public-facing tagline? (Optional, can be added later.)

---

## Batch 2 — Phase and scope (3 questions)

6. **Current phase** — Which best describes the current state?
   - `idea` — concept only, nothing built
   - `pre-MVP` — building toward first viable version
   - `MVP` — minimum viable product live or near-launch
   - `post-launch` — live with real users
   - `growth` — scaling beyond initial product-market fit

7. **Planning phase needed?** — Do you want a structured planning phase first (concept → design → build), or are you ready to start building immediately?

8. **Target audience** — Who is this for? (One sentence is enough at this stage.)

---

## Batch 3 — Technical (4 questions)

9. **Tech stack — known or to be decided?**
   - If known: list backend, frontend, database, key services
   - If TBD: what are the constraints? (language preference, hosting limits, budget, etc.)

10. **Repository structure** — Will this be:
    - `monorepo` (multiple packages in one repo)
    - `multi-repo` (separate repos per service)
    - `single` (one application, one repo)

11. **Hosting** — Where will this run?
    - If decided: name the provider
    - If TBD: what are the constraints? (geographic, regulatory, cost)

12. **Auth strategy** — How will users authenticate?
    - Email/password
    - OAuth (which providers?)
    - Passwordless / magic link
    - None (no user accounts yet)
    - TBD

---

## Batch 4 — Team and language (3 questions)

13. **Team size** — Solo, small (2-5), or larger?

14. **Working language preferences:**
    - Public-facing artifacts (README, marketing) — which language?
    - Internal artifacts (session logs, memory) — which language?
    - Chat with AI assistants — which language?
    - Closing-session command — how do you phrase "close the session" in your working language?

15. **Project visibility:**
    - Public repository (open source)
    - Private repository
    - Mixed (some components open, some closed)

---

## Batch 5 — Confidentiality (2 questions)

16. **Specific NDAs or partnerships** — Are there any agreements that require handling specific information carefully?

17. **Project-specific SECRET items** — Beyond the standard list (passwords, tokens, customer PII), are there specific things that should never appear in chats, commits, or public artifacts?
    - Trade secrets
    - Specific business model details
    - Partner names
    - Pricing strategy
    - Other

---

## Batch 6 — Roadmap (1 question)

18. **Roadmap** — Do you have a roadmap document?
    - Yes, located at: __________
    - No, would like to create one
    - Not needed at this stage

---

## After all questions answered

The AI assistant uses the answers to:

1. **Generate `PROJECT.md`** from `templates/PROJECT.template.md` with all placeholders filled in.
2. **Generate initial `TODO.md`** from `templates/TODO.template.md` with phase-appropriate tasks.
3. **Generate project `README.md`** from `templates/README.template.md`.
4. **Create `docs/sessions/` folder** (empty, ready for daily logs).
5. **Copy `MANIFESTO.md`** into `docs/`.
6. **Copy `CHANGELOG.template.md`** to project root as `CHANGELOG.md`.
7. **Suggest first session type** based on current phase:
   - Phase `idea` or `pre-MVP` → start with Thinking session (concept and architecture)
   - Phase `MVP` → start with Coding session (specific milestone)
   - Phase `post-launch` or `growth` → ask what the immediate focus is

---

## Question design principles

- **Open-ended where needed, multiple choice where possible** — reduces typing on mobile.
- **Batched** — 3-5 questions per round, not all 18 at once.
- **Defaults available** — if the founder says "skip" or "default" the AI uses sensible defaults from the template.
- **No assumptions about commercial intent** — works equally for hobby projects and ventures.
- **No team-size assumptions** — single-person and small-team projects covered uniformly.

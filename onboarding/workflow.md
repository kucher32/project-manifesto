# Onboarding workflow

> How the conversational onboarding flow works, step by step.
> This document explains the design behind `PROMPT.md` and `questions.md`.

---

## Overview

The onboarding flow is designed to take a founder from "I have a new project idea" to "I have a configured working environment with my AI assistant" in approximately 15 minutes of conversation.

It is intentionally conversational rather than form-based. Three reasons:

1. **AI assistants do conversation well.** A web form would require building and hosting it. A conversation requires only a prompt.
2. **Conversation allows clarification.** When a question is ambiguous, the founder can ask back.
3. **Conversation produces context.** The AI reads the answers, understands the project, and can immediately work in that context.

---

## The flow

```
┌─────────────────────────────────────────────────────────┐
│ 1. Founder downloads or clones project-manifesto        │
│    into a new project folder                            │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ 2. Founder opens AI assistant                           │
│    (Claude, ChatGPT, etc.)                              │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ 3. Founder pastes onboarding/PROMPT.md content          │
│    The AI reads MANIFESTO.md and template files         │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ 4. AI asks questions in 6 batches (~3-5 each)           │
│    Founder answers (~2 minutes per batch)               │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ 5. AI generates PROJECT.md, TODO.md, README.md,         │
│    CHANGELOG.md from templates with founder's answers   │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ 6. AI suggests the first session type based on phase    │
│    Founder confirms or adjusts                          │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ 7. Work begins. The framework is set up.                │
└─────────────────────────────────────────────────────────┘
```

---

## Question batching design

The 18 questions are split into 6 batches:

| Batch | Questions | Focus | Time estimate |
|---|---|---|---|
| 1 | 1-5 | Basics (name, brand, domain, description) | 2-3 min |
| 2 | 6-8 | Phase and scope | 1-2 min |
| 3 | 9-12 | Technical (stack, hosting, auth) | 3-5 min |
| 4 | 13-15 | Team and language | 1-2 min |
| 5 | 16-17 | Confidentiality | 2-3 min |
| 6 | 18 | Roadmap | 1 min |

**Total estimate:** 10-16 minutes for a founder who has thought about the project.

If the founder is still in pure idea phase, some questions will be answered as "TBD" — that is fine and expected. The AI records them in TODO for later resolution.

---

## What gets generated

After onboarding, the new project repository should contain:

```
{{PROJECT_NAME}}/
├── README.md              # Generated from README.template.md
├── PROJECT.md             # Generated from PROJECT.template.md
├── TODO.md                # Generated from TODO.template.md
├── CHANGELOG.md           # Generated from CHANGELOG.template.md
├── LICENSE                # (Founder adds based on visibility choice)
├── docs/
│   ├── MANIFESTO.md       # Copy of the framework manifesto
│   └── sessions/          # Empty folder, ready for daily logs
├── .gitignore             # (Founder adds based on stack)
└── .env.example           # (Founder adds based on stack)
```

The framework provides the **first four** files (README, PROJECT, TODO, CHANGELOG) and `docs/MANIFESTO.md`. The founder is responsible for `LICENSE`, `.gitignore`, `.env.example`, and any project-specific files.

---

## Defaults and shortcuts

The AI is instructed to accept these shortcuts in answers:

- **"skip"** — use the template default, mark as TBD in TODO
- **"default"** — same as skip
- **"same as before"** — if the founder is iterating, reuse the previous answer for that field
- **"see PROJECT.md"** — if the founder has already drafted some of this elsewhere

---

## Special situations

### Founder is uncertain about phase

If the founder cannot decide between `idea` and `pre-MVP`, default to `idea`. The phase can be updated in `PROJECT.md` later.

### Founder rejects a question

Some founders may push back: "Why do you need to know about NDAs?" The AI should explain the rationale (so it can flag confidentiality risks) but accept refusal and use defaults.

### Founder reveals SECRET-tier information

If a founder accidentally shares an API key, password, or other secret during onboarding, the AI should:

1. Pause immediately
2. Acknowledge what happened
3. Suggest rotating the credential
4. Continue onboarding without recording the secret anywhere

### Founder wants a planning phase first

If the founder answers question 7 with "yes, planning phase first," the AI proposes:

- First session: Thinking
- Goal of first session: produce a concept document
- TODO seeded with: "Draft concept document", "Identify target audience details", "Sketch initial architecture"

---

## Future automation

Currently, this flow is purely conversational. A founder pastes the prompt, the AI reads files, the conversation happens.

Planned automation:

- **A Claude skill** that triggers on "create a new project" and walks through the flow automatically
- **A CLI tool** (`pm init`) that clones the templates and opens the AI in the right context
- **A web app** that runs the conversation through a friendly UI

Contributions welcome.

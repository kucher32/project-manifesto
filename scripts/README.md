# Scripts

Automation helpers for `project-manifesto` adoption.

This folder is a placeholder at launch. As the framework matures, scripts will be added to make adoption faster.

---

## Planned scripts

### `init.sh` — Initialize a new project

A shell script that:

1. Clones or copies the manifesto and templates into a target directory
2. Renames template files (strips `.template` suffix)
3. Creates the `docs/sessions/` folder
4. Optionally generates a starter `.gitignore`

Usage (planned):

```bash
curl -sSL https://raw.githubusercontent.com/kucher32/project-manifesto/main/scripts/init.sh | bash -s -- my-new-project
```

Or:

```bash
git clone https://github.com/kucher32/project-manifesto.git /tmp/pm
bash /tmp/pm/scripts/init.sh my-new-project
```

### `sync.sh` — Sync manifesto with master

For projects that adopted the manifesto and want to pull updates:

```bash
bash scripts/sync.sh
```

Fetches the latest `MANIFESTO.md` from the master repo and updates the local copy with a diff preview.

### `session-start.sh` — Begin a new session log

Creates `docs/sessions/YYYY-MM-DD.md` from `templates/session-log.template.md` with autopopulated date and (optionally) project name from the parent folder name.

### `session-close.sh` — Help with the closing prompt

Helper that gathers `git diff --stat`, the current `TODO.md` state, and recent commits to seed a closing-prompt context for the AI.

### `session-retroactive.sh` — Reconstruct a missed handoff

When a previous Claude Code session ended without a closing prompt, this helper produces a draft handoff by reading `git log`, `git diff`, and the `TODO.md` delta since the last session log.

### `session-precommit-hook.sh` — Optional pre-commit check

Optional Git hook that refuses to commit if there are changes today but no `docs/sessions/YYYY-MM-DD.md` entry. Disabled by default.

---

## Future automation

Beyond scripts, the long-term plan includes:

- **A Claude skill** — triggered by "create a new project" in a Claude conversation
- **A CLI tool** (`pm init`, `pm sync`) — installable via npm or homebrew
- **A web app** — guided onboarding through a friendly UI

Contributions are welcome. Open an issue to discuss before opening a pull request for major additions.

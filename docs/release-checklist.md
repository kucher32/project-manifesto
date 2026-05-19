# Release checklist

> Used by the Architect when formulating release prep step prompts. Read this before issuing any release-prep step. Failure to follow this checklist is what produced the v0.6.0 README catch-up commit — codify so it does not repeat.

## Pre-release (private repo)

1. **Bump MANIFESTO version header.** Update `**Version:**` and `**Last updated:**` fields. Semver: minor for new roles or principles, patch for clarifications or fixes only.

2. **Add CHANGELOG entry.** Condensed format, grouped by theme. Sections: Added / Changed / Removed / Notes (Notes captures deferred items, validation status, breaking-change warnings).

3. **Update README.md.**

   - Core principles at a glance — add a bullet for any new principle or role.
   - What you get → templates/ list — add any new template files.
   - Workflow roles bullet — update if a new role was added.
   - Badges in header — update status if needed (pre-1.0 → 1.0+).

4. **Update Layer 2 (.claude/instructions.md) if any new canonical file was added.** "What you read and where it lives" section should reference it.

5. **Update PROJECT.template.md if any new project-specific configuration is required.** New section if a new role needs declaration.

6. **Bundle commit + push to origin.** Single commit for the entire release-prep edit bundle.

7. **Create annotated git tag.** `git tag -a vX.Y.Z -F /tmp/tag-msg.txt`. Tag message includes the highlights — adopters read tag messages on GitHub releases page.

8. **Push tag to origin.** `git push origin vX.Y.Z`.

## Public release (private → public mirror)

9. **Create release branch from main.** `git checkout -b release/vX.Y.Z`.

10. **Generate diff for Operator review.** `git diff public/main..origin/main > _inbox/release-vX.Y.Z-diff.txt`. The `_inbox/` directory is gitignored — diff stays local.

11. **Pause for Operator review.** Pack R pause checkpoint. Operator reads the diff, flags any private content that must not go public (session logs, _drafts/, _inbox/, CLAUDE.md, OPERATING.md depending on policy). Without explicit Operator "go", no public push happens. This is a hard rule from OPERATING.md.

12. **Sanitization, if needed.** Remove any flagged files from the release branch via `git rm` before the public push. Document what was removed in the step report.

13. **Push release branch to public main.** `git push public release/vX.Y.Z:main`. Never `--force`.

14. **Push tag to public.** `git push public vX.Y.Z`.

15. **Delete local release branch.** `git branch -d release/vX.Y.Z`.

## Post-release

16. **Update session log.** Done entry includes both private and public push completion.

17. **Update Next section.** Any deferred items from release Notes go here as Session N+1 candidates.

## Notes

- This checklist supersedes ad-hoc release prep. Architect reads it at the start of every release-prep step formulation. If a checkpoint here was missed during a release, the next release adds a step to address it (e.g., README catch-up commit after v0.6.0 because README was missed in Step 06).
- The Pack M sanitize-script will eventually automate steps 10-12 (diff generation, common sensitive-file removal). Until then, manual review per OPERATING.md.
- Adopters with public-only repos (no private/public split) skip steps 9-15 and use only the pre-release section.

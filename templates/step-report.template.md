# Step {{NN}} — {{step-name}}

**Date:** {{YYYY-MM-DD HH:MM}}
**Task:** {{task-slug}}

## Goal

{{What this step needs to accomplish, in one paragraph. Usually copied or summarized from the Architect's prompt.}}

## What was done

1. {{Numbered list of actions taken, in order.}}

## Decisions made during work

{{Deviations from the original plan, or one short line stating there were none.}}

## Files created / modified

| File | Action | Lines / bytes |
|---|---|---|
| {{path}} | {{created / modified / deleted}} | {{numbers or N/A}} |

## Status

{{One sentence describing current state. Examples:
"Done — awaiting Architect review."
"Done — awaiting Architect re-review before commit."
"Blocked — need Operator decision on X."
"Partial — implemented A and B; C requires a separate step."}}

---

<!-- Optional sections below. Delete what you don't use. -->

## Model escalation request

<!-- Required only when Status mentions escalation. Omit otherwise. -->

- **Current model + effort:** {{e.g. sonnet, effort low}}
- **Attempts:** 2
  - Attempt 1: {{one line — what failed}}
  - Attempt 2: {{one line — what failed}}
- **Recommended next:** {{e.g. opus, effort medium}}
- **Reason:** {{one line — why current tier isn't enough}}

## Repository state

```
git status:
{output}
git log -3 --oneline:
{output}
```

## Decisions

- **{{decision}}**: {{rationale}}

## Open questions

- 

## Operator decision needed

<!-- Use this section when a step uncovered an ambiguity that must be resolved by the Operator before continuing. -->

- 

## Notes

<!-- Free-form. Anything that doesn't fit above and is worth carrying forward. -->

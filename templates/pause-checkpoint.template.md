# Pause checkpoint — Step {{NN}} — {{descriptor}}

## Pause reason

{{One paragraph: what caused the pause, what gate is open. Examples: "Awaiting Operator review of dry-run output before destructive rm." / "Multiple valid continuations; Operator must choose."}}

## What was completed before pause

1. {{Sub-task done, with verifiable output. Example: "Ran `git status` — clean working tree, on branch main."}}
2. {{...}}
3. {{...}}

## What is pending after resume

1. {{Remaining sub-task. Example: "Run `rm -rf ./scratch/` if Operator confirms."}}
2. {{...}}

## Output captured at pause

    {{Verbatim terminal output the Operator is expected to evaluate. Indent with four spaces for fenced-equivalent rendering. ANSI escape codes may be stripped; note if so.}}

## What Runner needs to resume

{{What kind of input the Operator should give, and the acceptable forms. Example: "Any of: 'go', 'proceed', 'delete it' to continue with rm. Any of: 'abort', 'stop', 'cancel' to skip the deletion and write the final step report without it."}}

## Status

Paused. {{One-sentence current state. Example: "Awaiting Operator go-ahead on destructive rm at scratch/ directory."}}

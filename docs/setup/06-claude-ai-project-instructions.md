# Claude Desktop Project Instructions — installation guide

Layer 2 enforcement for project-manifesto development. The canonical
text for Claude Desktop's Project Instructions field lives at
.claude/instructions.md in the repository root. This document explains
what Layer 2 is, where to find the text, how to install it, and when
to update it.

Prerequisites: a working Claude Desktop installation with this
repository as a configured project. See docs/setup/01-prerequisites.md
for environment setup.

## What Layer 2 does

Claude Desktop's Project Instructions field auto-loads its content
into every chat opened inside the project. Unlike a typed
session-opening prompt, this content is available to the Architect
from the first token of the first response — before MCP filesystem
may have activated, before any explicit Operator setup messages.

For project-manifesto work, Layer 2 carries the minimum runtime law
that lets the Architect function correctly from the first turn of
any new chat: who the roles are, where the working repository lives,
how signals like "готово" are interpreted, the disagreement protocol,
the MCP read-only role convention, the prompt-wrapping convention,
and pointers to canonical sources on disk.

Layer 2 is the primary enforcement surface in Claude Desktop. It is
not optional reinforcement of Personal Preferences — it carries the
law directly.

## Where the canonical text lives

The text to paste into Claude Desktop is in this repository at:

    .claude/instructions.md

The .claude/ directory is a project-level convention for
Claude-related artifacts. Currently it holds only instructions.md;
future Claude-related project artifacts (configuration, scripts,
templates specific to this project) will live alongside.

The canonical text is the entire contents of .claude/instructions.md.
No BEGIN/END markers, no preamble, no extraction needed — the whole
file is the Instructions block.

## How to install in Claude Desktop

1) Open Claude Desktop on your machine.

2) Navigate to the Project Manifesto project (or your project's
   equivalent — Layer 2 applies per-project).

3) Open Project Instructions (the field for project-scoped context).
   The exact UI location depends on Anthropic's current Claude Desktop
   version. Look for a Settings or Configuration area within the
   project view.

4) Open .claude/instructions.md from this repository in any text
   editor.

5) Select all content of .claude/instructions.md and copy it.

6) Paste into Claude Desktop's Project Instructions field.

7) Save.

8) Open a fresh chat inside the project and verify the Architect
   starts with project-specific context (knows the working repo path,
   knows the roles, recognizes the "готово" signal).

## How to verify Layer 2 is working

In any fresh chat inside the project, type:

    What repository am I working against?

A correctly-installed Layer 2 produces an answer naming the private
working copy path verbatim. A missing or stale Layer 2 produces
either no specific answer or a hallucinated path.

A second verification: type "готово" to a Layer-2-equipped Architect
without prior context, and observe whether the Architect responds
with confusion or with a request for the step report to read. The
latter indicates Layer 2 carries the signal interpretation correctly.

## When to update Layer 2

The .claude/instructions.md content is project-specific and is
expected to evolve. Update when:

- The working repository path changes (move to different machine,
  repository rename, branch model change).

- Role definitions in the project change (additional roles like
  Tester, Translator).

- Working language or signal conventions change.

- The MCP activation pattern, or any other Claude Desktop runtime
  behavior, changes such that the existing Layer 2 instruction needs
  adjustment.

- Wrapping conventions for prompts are updated based on new
  experimental findings.

After updating .claude/instructions.md in the repository, you must
re-paste the updated content into Claude Desktop's Project
Instructions field. Claude Desktop does not auto-sync with the file
on disk; the paste is manual.

## How Layer 2 interacts with other enforcement layers

Project-manifesto has three enforcement layers ordered by descending
scope:

- Layer 1 = Personal Preferences in Claude Desktop Settings,
  account-wide, applies to every chat regardless of project.

- Layer 2 = Project Instructions, project-wide, applies to every
  chat opened inside the specific project. This document covers
  Layer 2.

- Layer 3 = Session-opening prompt typed by the Operator at the
  start of a fresh chat, chat-wide, manually invoked.

In Pack N v2 (current architecture), Layer 2 is the primary
enforcement surface for project-manifesto work because Claude
Desktop's Project Instructions are the most reliable auto-load
channel. Layer 1 and Layer 3 are supplementary; their roles are
under audit pending real-use validation.

## Limitations

Project Instructions in Claude Desktop are subject to whatever
Anthropic's current implementation supports. Token limits, formatting
restrictions, or rendering quirks of the Instructions field may
change without notice. If a previously-working Layer 2 stops
behaving correctly, suspect a Claude Desktop update before
suspecting the .claude/instructions.md content.

## References

- .claude/instructions.md — the canonical Layer 2 text itself.

- docs/setup/02-claude-ai-setup.md — Layer 1 Personal Preferences
  (under audit pending Layer 2 sufficiency validation).

- START-HERE.md — Layer 3 session-opening prompt, at repository root.

- OPERATING.md — two-repository workflow (private working copy +
  public release mirror).

- MANIFESTO.md — full framework rules.

# MCP filesystem setup

> Give Claude.ai (the Architect) read access to your project's step reports
> via the Model Context Protocol filesystem server.
> Estimated time: 10-15 minutes (mostly authentication and verification).
>
> Prerequisites: complete [`01-prerequisites.md`](./01-prerequisites.md)
> through [`03-claude-code-setup.md`](./03-claude-code-setup.md) first.

## Why MCP matters in this framework

Pack H — step reports between the Executor and the Architect — works in
two modes:

- **With MCP filesystem.** The Executor writes step reports to disk; the
  Architect reads them directly via MCP. Zero copy-paste between Claude
  Code's terminal and Claude.ai's chat. The Operator's role narrows to
  signaling "готово" and reviewing verdicts.

- **Without MCP filesystem.** The Executor still writes step reports, but
  the Operator must copy-paste them from terminal into Claude.ai. This
  works but is fragile: long files truncate, paging artifacts creep in,
  it is easy to forget which step was last shared.

The framework runs fine in either mode. MCP-mode is strictly faster and
less error-prone once configured. Setup time amortizes across every
session you ever run.

## What MCP is

The **Model Context Protocol** is an open standard from Anthropic for
giving AI clients (like Claude.ai) structured access to external context:
files on your machine, databases, custom services. An **MCP server**
exposes a specific capability; the **MCP client** (your Claude.ai
account, in this case) connects to it and gains the capability.

For project-manifesto work, you need one specific server: the
**filesystem server**, which lets Claude.ai read (and optionally write)
files on a specific directory you authorize.

Reference: [modelcontextprotocol.io](https://modelcontextprotocol.io).

## What to install

### Authoritative documentation

Anthropic maintains the canonical setup guide for MCP with Claude.ai.
The setup steps change as the product evolves, so this document
deliberately defers to the upstream source:

- [Anthropic's MCP documentation](https://docs.anthropic.com/en/docs/build-with-claude/computer-use)
  → look for the MCP / Claude.ai integration section.
- [Claude.ai MCP setup guide](https://support.anthropic.com) → search
  for "MCP filesystem".
- [modelcontextprotocol.io](https://modelcontextprotocol.io) →
  filesystem server reference and example configurations.

If a link above moves, search Anthropic's documentation for "MCP
filesystem Claude.ai" — the topic is stable even if URLs are not.

### What you are configuring

At a high level, MCP filesystem setup involves three things:

1. **Install the filesystem MCP server** on your machine. This is a
   small program (typically Node.js) that exposes a directory tree via
   the MCP protocol.

2. **Authorize a specific directory.** You point the server at your
   project directory (e.g., `/home/you/projects/my-project`). Claude.ai
   will only see files under this path.

3. **Connect Claude.ai to the server.** This is a one-time configuration
   in Claude.ai's settings or developer console, pairing your account
   with the running MCP server.

The Anthropic documentation walks each of these in detail.

## Choosing a directory to authorize

**Authorize the project root, not your home directory.** If you point
MCP at `/home/you/`, the Architect can read every file in your home
folder — including SSH keys, credentials, browser history, unrelated
projects. This is a security mistake.

For project-manifesto work, the right directory is the project root
that contains:

- `MANIFESTO.md` / `RATIONALE.md` / `CHANGELOG.md`
- `docs/sessions/`
- `_drafts/` (if you use them)
- Your own project source files

For a private/public split (Pack M), authorize the **private** working
repository. The public mirror is release-only and read-only from the
Architect's perspective anyway.

If you work on multiple projects, you can authorize multiple directories
to the same MCP server, or run separate MCP server instances per project.
The Anthropic documentation covers both patterns.

## Verifying MCP is working

After setup, in a new Claude.ai chat, ask the Architect:

> Can you read the MANIFESTO.md file in my project via MCP? Reply with the version number on the first line.

A correctly-configured Architect uses its MCP filesystem tool to read
the file and reports the version (e.g., "Version: 0.6.0"). A
misconfigured Architect either says it cannot read the file, or
hallucinates a version it cannot verify.

If verification fails, the common causes are:

- **MCP server not running.** Check that the server process is up
  (`ps aux | grep mcp-filesystem` or similar).
- **Wrong directory authorized.** Check the server's config: the
  authorized path must contain your `MANIFESTO.md`.
- **Claude.ai not connected to the server.** Re-check the connection
  in Claude.ai settings.
- **Permission issues.** The server runs as your user; it must have
  read access to the project directory. Check `ls -la` on the project
  root.

## Architect convention — read-only MCP

By project-manifesto convention, the Architect's MCP filesystem access
is treated as **read-only**, even if the server is technically configured
for read-write. The Architect does not write files via MCP; all write
operations are formulated as prompts for the Executor (Claude Code) to
run in its own shell.

This convention exists because the role boundary in `MANIFESTO.md` § 4
defines the Architect as a planner and reviewer, not an executor. MCP
write access at the tool level is fine — the discipline is at the
Architect's behavior level (and is encoded in Personal Preferences rule
1, see [`02-claude-ai-setup.md`](./02-claude-ai-setup.md)).

If you find your Architect writing files via MCP, redirect it: "это
работа Executor'а, формулируй промпт." The convention preserves the
audit trail that makes Pack H reports meaningful — every file change
in your project flows through an Executor step report.

## When MCP is not available

You can adopt the manifesto without MCP and operate fully via copy-paste.
This is slower but correct. The session-opening prompt
(see [`START-HERE.md`](../../START-HERE.md)) is designed to work in
both modes — the Architect detects MCP availability and adjusts.

If you operate without MCP:
- Step reports still go to disk per Pack H (the Executor writes them).
- The Operator copies them from `cat docs/sessions/{date}-{slug}/NN-step.md`
  output into the Claude.ai chat after each step.
- Pause checkpoints (Pack R) are similarly copy-pasted.
- Pack S verification gates use the pasted content rather than direct
  MCP reads.

The framework's value is in the discipline, not in the channel. MCP is
a productivity multiplier, not a prerequisite.

## After this setup

Continue to [`05-workflow-two-windows.md`](./05-workflow-two-windows.md)
for the day-to-day operational pattern.

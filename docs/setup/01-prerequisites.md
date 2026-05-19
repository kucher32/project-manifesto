# Prerequisites

> What you need before starting setup. Estimated time to read: 5 minutes.
> Estimated time to acquire missing prerequisites: varies (10 minutes
> for installing Claude Code; longer if you need to sign up for a plan).

## Required

### Claude.ai Pro or Max subscription

You need a paid plan. The free tier of Claude.ai does not support the
features this framework depends on, and the rate limits make a Pack-compliant
multi-step session impractical.

- **Pro** ($20/month at time of writing): sufficient for most solo founders
  on most days. Some longer multi-step sessions may hit the daily limit
  late in the day; in practice this becomes a natural session boundary.
- **Max** ($100-200/month at time of writing): rate limits are large
  enough that they almost never block. Recommended if you work full-time
  on a project using this framework.

Pricing changes; check [claude.ai/upgrade](https://claude.ai/upgrade) for
current figures. The framework itself does not depend on which tier you
choose — only that you have one of them.

### Claude Code

The CLI tool that serves as the Executor. Install per Anthropic's
documentation:

- [Claude Code installation guide](https://docs.anthropic.com/en/docs/claude-code/overview)

Quick install on most systems:

```bash
npm install -g @anthropic-ai/claude-code
```

After install, run `claude` in any directory to verify it launches. The
first launch will walk you through authentication.

### Local git

You need `git` installed and configured with your name and email:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Most modern operating systems either include git or make it a one-line
install. macOS: `brew install git`. Ubuntu/Debian: `sudo apt install git`.
Windows: install Git for Windows or use WSL2.

### A working terminal

Pack-compliant work runs in a terminal in parallel with a browser window.
On Linux and macOS this is built in. On Windows, **WSL2** (Windows
Subsystem for Linux) is strongly recommended — many of the shell-hygiene
rules in the manifesto were validated on WSL2, and pure Windows shells
(cmd, PowerShell) behave differently around quoting and heredocs.

WSL2 install: [learn.microsoft.com/en-us/windows/wsl/install](https://learn.microsoft.com/en-us/windows/wsl/install).

### A text editor

Any editor that handles markdown comfortably. VS Code is common; the
framework has no editor lock-in. The Operator opens session logs and
step reports in the editor to read and occasionally to spot-check what
the Executor wrote.

## Strongly recommended

### MCP filesystem server

The Model Context Protocol (MCP) filesystem server is what lets
Claude.ai (the Architect) read step reports directly from your local
disk. Without MCP, the Architect can still work, but every step report
must be copy-pasted from the terminal into the chat — which is exactly
the friction that motivated Pack H.

Setup instructions are in [`docs/setup/04-mcp-setup.md`](./04-mcp-setup.md).
Anthropic's official documentation:
[modelcontextprotocol.io](https://modelcontextprotocol.io).

You can complete initial setup without MCP and add it later. The
session-opening prompt detects MCP availability and adjusts.

## Optional

### GitHub account

The manifesto repository itself lives on GitHub, but your own project
using this framework does not have to. Local git is sufficient if you
work alone and back up your machine. GitHub becomes useful when:

- You collaborate with others.
- You want off-machine backup with version history.
- You want CI/CD or other GitHub-integrated tooling.

If you want to publish your own project under this framework, GitHub
(or any git host) is straightforward. The framework does not prescribe
a host.

### A private/public repo split (Pack M)

If your project will have any confidential content (client names,
unreleased pricing, internal architecture, draft public communications),
the manifesto recommends a private working repository with a separate
public mirror — see `OPERATING.md` in this project for a worked example.
Pack M (the private/public split protocol) is in active formalization
and not strictly required for v0.6.0 adoption, but the pattern is
already proven in practice.

You can adopt the manifesto without Pack M and add it later as your
project's content sensitivity grows.

## What you do NOT need

- **A separate AI orchestration tool.** Claude.ai + Claude Code + MCP
  filesystem cover the entire workflow. No agent framework, no LangChain,
  no n8n.
- **Cloud hosting.** All Pack-compliant work happens locally. You may
  deploy your project to the cloud, but the framework runs on your own
  machine.
- **A team.** The framework is designed for solo founders. It scales to
  small teams by extension (see MANIFESTO § 4 "Extending the role model"),
  but the default audience is one human + two AI roles.
- **JavaScript, Python, or any specific stack.** The manifesto governs
  workflow, not tech choice. The framework has been validated on projects
  using TypeScript, Python, Bash, and pure documentation.

## Verifying your environment

Before continuing to [`02-claude-ai-setup.md`](./02-claude-ai-setup.md),
confirm:

```bash
claude --version       # Claude Code is installed
git --version          # git is installed
git config user.name   # git is configured
git config user.email  # git is configured
```

If you are on Windows, run all of the above inside WSL2, not in cmd or
PowerShell.

If all four commands return successfully, you are ready for the next
step.

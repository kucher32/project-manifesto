# MCP filesystem setup for Claude Desktop

Optional setup. The Architect role in this framework operates in Claude Desktop. When MCP filesystem access is configured, the Architect reads step reports, canonical files (MANIFESTO.md, RATIONALE.md, templates), and session logs directly from the working repository. Without it, the workflow falls back to the Operator copy-pasting terminal output between Claude Code (Executor) and Claude Desktop (Architect). Pack H step reports remain the canonical state record either way; MCP just removes a manual step.

This document covers the maintainer's real WSL2 setup in detail and points to the official Anthropic documentation for everything else. Adopters on other platforms adapt by analogy.

## Config file location

Claude Desktop reads `claude_desktop_config.json` from a platform-specific path:

| Platform | Path |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/claude-desktop/claude_desktop_config.json` |

The shortcut to open the file from the app: Settings → Developer → Edit Config. Claude Desktop creates the file if it does not exist.

## Minimal config

A single filesystem MCP server entry exposing one project directory:

    {
      "mcpServers": {
        "filesystem": {
          "command": "npx",
          "args": [
            "-y",
            "@modelcontextprotocol/server-filesystem",
            "/absolute/path/to/your/project"
          ]
        }
      }
    }

The last argument is the absolute path to the directory the server exposes. Use only directories you trust the Architect to read. Multiple paths can be passed as additional arguments.

After saving, fully quit Claude Desktop (not minimize, not close-window — actually quit) and relaunch. The server is loaded on app start.

## WSL2 setup (maintainer's working configuration)

The framework maintainer develops in WSL2 Ubuntu on a Windows host. Claude Desktop is a Windows application — even when the project lives inside WSL, the Claude Desktop config and the Node.js process that runs the MCP server both live on the Windows side.

Concretely:

1) Config file lives at `%APPDATA%\Claude\claude_desktop_config.json` (the Windows-side path), not inside WSL.

2) The working repository is on a Windows drive that WSL exposes under `/mnt/<drive>/...`. The same directory is reachable from Windows by its native path (e.g., `E:\PROJECTS\...`) or from WSL by its mount path (e.g., `/mnt/e/PROJECTS/...`).

3) In the config `args` array, the path is given in the WSL mount form because the filesystem MCP server resolves it through the WSL interop layer. The `command` is `wsl.exe` with `-e` to invoke `npx` inside the WSL environment. Example for a project at `E:\PROJECTS\<your-project>`:

    {
      "mcpServers": {
        "filesystem": {
          "command": "wsl.exe",
          "args": [
            "-e",
            "npx",
            "-y",
            "@modelcontextprotocol/server-filesystem",
            "/mnt/<drive>/PROJECTS/<your-project>"
          ]
        }
      }
    }

4) `npx` is resolved from the WSL-side Node.js installation. Node.js must be installed inside WSL (not the Windows-side Node.js) for this pattern to work.

This setup is the maintainer's daily-driver configuration; it has been used for every Architect session in this project. It is not the only valid WSL2 configuration — alternatives (running the Windows-side npx without wsl.exe, using UNC paths like `\\wsl$\Ubuntu\...`) may also work but have not been verified by the maintainer.

## Verification

Two ways to confirm MCP is active:

1) In Claude Desktop, Settings → Developer shows the filesystem server with a "connected" indicator.

2) In a fresh Architect chat with MCP available, asking the model to list available tools (or simply attempting to read a file from the working repository) succeeds without falling back to "I do not have filesystem access".

If neither check passes after a full app quit + relaunch, check the JSON syntax, verify all paths are absolute, and consult the official troubleshooting guide.

## For other platforms

macOS, Windows native, plain Linux, or non-WSL2 setups: use the path from the table above and follow the minimal config pattern. For platform-specific troubleshooting (permissions, logs, security prompts), see the official Anthropic documentation at https://support.claude.com/en/articles/10949351-getting-started-with-local-mcp-servers-on-claude-desktop.

## Related files

`.claude/instructions.md` — Layer 2 runtime instructions for the Architect role. When MCP is active, the Architect reads canonical project files directly via filesystem instead of waiting for copy-pasted content.

# setup.md — manifesto v0.7.3 installation specification

This file is the installation specification for the project-manifesto v0.7.3 framework. It describes the six-phase install procedure that an AI assistant (typically Claude Code) performs on the user's behalf to install the framework into a project directory.

## How to use this file

This install specification works on any platform where Claude Code runs: macOS,
Linux (native), WSL2, and Windows native. Step 6 (MCP configuration) contains
platform-specific branches that detect the platform automatically.

Before performing any operation described below, the assistant must obtain explicit consent from the user. Print this confirmation prompt and wait for an affirmative reply:

    [33m?[0m project-manifesto v0.7.3 install — ready to start.

    This will add these files to your project:
      - CLAUDE.md, BACKLOG.md, PROJECT.md, .claude/instructions.md
      - .claude/sessions/, .claude/inbox/, .claude/state/ (empty folders)
      - For mode-1 (Triad): also MANIFESTO.md and claude-desktop-prefs.md
      - For mode-1 (Triad): MCP config entry in Claude Desktop settings

    Your existing files are not touched.

    Start install? [y/N]

If the user replies with y, yes, or affirmative equivalent: proceed with Step 1 through Step 6 below.

If the user replies with anything else: stop. Do not perform any operation. The user can run setup again later if they change their mind.

## Notes on execution after consent

After the user has consented:

Output language: detect the language the user used when invoking this file
and use that language for ALL messages printed to the user throughout the install
(questions, status lines, confirmations, summaries, error messages, hand-off text).
File names, technical terms (git, python3, npx, MCP, CLAUDE.md, etc.) and file
content written to disk stay in English regardless of user language.
If the user wrote in Russian — every user-facing sentence is in Russian.
If the user wrote in English — every user-facing sentence is in English.

The six phases below describe the install in fixed order. Steps 1, 4, and 5 run for both modes. Step 6 runs only for mode-1-triad.

Within each phase, perform the listed operations directly. Print one-line status (cyan → for an operation that started, green ✓ for an operation that completed) so the user sees progress.

Halt and ask the user only at three categories of decision points:

1) Authorial choices that depend on the user's intent: which mode to install (Step 2).

2) Operations the assistant cannot perform on the user's behalf: pasting content into Claude.ai Settings UI, installing Claude Desktop, installing Node.js when administrative privileges are required.

Outside these three categories, perform operations as part of the install — do not ask permission for each one. The user already consented at the start.

If the user did not consent at the start, none of the operations in Step 1-6 should run. The consent prompt is the gate.


## Setup workflow overview

Six phases in fixed order:

1) Pre-flight checks — verify the install can proceed.
2) Mode selection — ask Operator which mode (solo or triad).
3) Project state detection — notify if existing project; no file changes either way.
4) Install — copy template files to project root with placeholder substitution.
5) Hand-off — print summary, point Operator at next actions.
6) MCP filesystem helper (mode-1-triad only) — configure Claude Desktop MCP.

Step 6 runs after Step 5 when SELECTED_MODE is mode-1-triad. Skipped otherwise.


## Step 1 — Pre-flight checks

Two checks. Both lightweight. No halt-and-ask unless something is wrong.

0) Verify git is installed.

    command -v git >/dev/null 2>&1 && echo -e "\033[32m✓\033[0m git found: $(git --version)" || { echo -e "\033[31m✗\033[0m git not found. Install git and retry."; exit 1; }

If git is not found: halt immediately. Do not proceed. Tell Operator:
"git is required for this install. Install it from https://git-scm.com and re-run setup."

0b) Verify python3 is installed.

    command -v python3 >/dev/null 2>&1 && echo -e "\033[32m✓\033[0m python3 found: $(python3 --version)" || { echo -e "\033[31m✗\033[0m python3 not found. Install Python 3 and retry."; exit 1; }

If python3 is not found: halt immediately. Tell Operator:
"python3 is required for this install.
  macOS/Linux: python3 is usually preinstalled. Try: python3 --version
  Windows: install from https://python.org/downloads and check 'Add to PATH'.
Re-run setup after installing."

1) Verify .manifesto/ folder exists at the current directory.

    test -d .manifesto && echo -e "\033[32m✓\033[0m .manifesto/ folder found" || { echo -e "\033[31m✗\033[0m .manifesto/ folder not found at $(pwd). Setup cannot proceed."; exit 1; }

If the folder is not found, halt and tell Operator: ".manifesto/ folder is missing from the current directory. Are you in the right place? Setup needs setup.md and .manifesto/ together at the project root."

2) Verify or initialize git.

    git rev-parse --is-inside-work-tree 2>/dev/null

If output is "true": git is already initialized.

    echo -e "\033[32m✓\033[0m git repository detected"

If output is not "true" (no git): initialize git directly. Initializing git in an empty or untracked directory is safe.

    echo -e "\033[36m→\033[0m initializing git"
    git init -q
    echo -e "\033[32m✓\033[0m git initialized"

3) Detect project path and Operator name for Step 4 substitutions.

    PROJECT_PATH=$(pwd)
    OPERATOR_NAME=$(git config user.name 2>/dev/null || echo "")

If OPERATOR_NAME is empty, ask Operator (the only Step 1 halt point if it triggers):

    Operator, git config does not have user.name set. What name should I record as Operator in this project's manifesto files?

Wait for reply. Save as OPERATOR_NAME. Continue.

    echo -e "\033[32m✓\033[0m PROJECT_PATH = $PROJECT_PATH"
    echo -e "\033[32m✓\033[0m OPERATOR_NAME = $OPERATOR_NAME"

Step 1 done.


## Step 2 — Mode selection

Ask Operator which manifesto mode to install. This is an authorial choice — halt and wait for reply.

Print the question:

    \033[33m?\033[0m Choose manifesto mode:

      1   mode-1 (Triad)
          Architect lives in Claude Desktop. Executor is Claude Code.
          You paste prompts between them. Higher discipline, higher capability.
          Good for: multi-day projects, complex state, multi-task continuity.

      2   mode-2 (Solo)
          Claude Code plays both planner and executor roles.
          Lower barrier to entry. Good for: small projects, single-day work.

    Reply: 1 or 2

Wait for Operator reply. Parse:
- Reply contains "1" → SELECTED_MODE = "mode-1-triad"
- Reply contains "2" → SELECTED_MODE = "mode-2-solo"
- Anything else → re-ask the same question

    echo -e "\033[32m✓\033[0m SELECTED_MODE = $SELECTED_MODE"

Step 2 done.


## Step 3 — Project state detection

Detect whether the project has existing content beyond the install distribution files.

    EXISTING=$(ls -A | grep -v '^\.manifesto$' | grep -v '^\.git$' | grep -v '^setup\.md$' | grep -v '^LICENSE$' | grep -v '^README\.md$' | head -5)

If $EXISTING is empty:

    echo -e "\033[32m✓\033[0m empty project — manifesto files will be installed at root"

If $EXISTING contains entries:

    echo -e "\033[32m✓\033[0m existing project detected — manifesto files will be added alongside existing content"
    echo -e "   Your existing files are not touched during install."
    echo -e "   After install, open an Architect chat — it will read your project"
    echo -e "   and help you organize tasks and project info."

No questions, no file moves, no analysis. Proceed directly to Step 4.

Step 3 done.


## Step 4 — Install template files

Copy files from .manifesto/{SELECTED_MODE}/ to project root, with placeholder substitution. Two explicit branches depending on SELECTED_MODE. No halt-and-ask in this phase — Claude Code executes directly.


### Step 4 — mode-1-triad branch (if SELECTED_MODE = "mode-1-triad")

Use this Python snippet exactly:

    python3 << 'PYEOF'
    from pathlib import Path
    import subprocess

    PROJECT_PATH = "<PROJECT_PATH from Step 1>"
    OPERATOR_NAME = "<OPERATOR_NAME from Step 1>"
    PROJECT_NAME = Path(PROJECT_PATH).name

    try:
        REPO_URL = subprocess.check_output(["git", "config", "--get", "remote.origin.url"], stderr=subprocess.DEVNULL).decode().strip()
    except subprocess.CalledProcessError:
        REPO_URL = "(no remote configured)"

    substitutions = {
        "{{PROJECT_PATH}}": PROJECT_PATH,
        "{{PROJECT_NAME}}": PROJECT_NAME,
        "{{REPO_URL}}": REPO_URL,
        "{{OPERATOR_NAME_OR_HANDLE}}": OPERATOR_NAME,
        "{{ONE_PARAGRAPH_PROJECT_DESCRIPTION}}": "(adopter: fill in a one-paragraph description of this project)",
        "{{WORKING_LANGUAGE_NOTE}}": "(adopter: state the working language convention for this project)",
        "{{PROJECT_SPECIFIC_OBLIGATIONS_OR_EMPTY}}": "",
        "{{PROJECT_SPECIFIC_EXTENSIONS_OR_EMPTY}}": "",
        "{{PROJECT_DESTRUCTIVE_OPS_OR_EMPTY}}": "",
        "{{ADDITIONAL_SIGNALS_OR_EMPTY}}": "",
        "{{ADDITIONAL_STATE_RECORDS_OR_EMPTY}}": "",
        "{{PROJECT_INSTRUCTIONS_CONTEXT_BLOCK}}": (
            "Project context (this Claude.ai Project specifically):\n"
            f"Working repository: {PROJECT_PATH}\n"
            "Pre-flight reads from this path. Do not scan other mounted directories."
        ),
    }

    targets = [
        (".manifesto/mode-1-triad/CLAUDE.md.template", "CLAUDE.md"),
        (".manifesto/mode-1-triad/MANIFESTO.template.md", "MANIFESTO.md"),
        (".manifesto/mode-1-triad/claude-desktop-prefs.md.template", "claude-desktop-prefs.md"),
        (".manifesto/mode-1-triad/.claude/instructions.md.template", ".claude/instructions.md"),
    ]

    for src, dst in targets:
        content = Path(src).read_text(encoding='utf-8')
        for placeholder, value in substitutions.items():
            content = content.replace(placeholder, value)
        Path(dst).parent.mkdir(parents=True, exist_ok=True)
        if Path(dst).exists():
            print(f"\033[33m!\033[0m {dst} already existed — overwritten with fresh template")
        Path(dst).write_text(content, encoding='utf-8')
        print(f"\033[32m✓\033[0m wrote {dst}")

    for d in [".claude/sessions", ".claude/inbox", ".claude/state"]:
        Path(d).mkdir(parents=True, exist_ok=True)
        Path(f"{d}/.gitkeep").touch()
        print(f"\033[32m✓\033[0m created {d}/")
    PYEOF

After Python completes, create empty state files if absent:

    test -e BACKLOG.md || { touch BACKLOG.md; echo -e "\033[32m✓\033[0m created BACKLOG.md (empty placeholder)"; }
    test -e PROJECT.md || { touch PROJECT.md; echo -e "\033[32m✓\033[0m created PROJECT.md (empty placeholder)"; }


### Step 4 — mode-2-solo branch (if SELECTED_MODE = "mode-2-solo")

Use this Python snippet exactly:

    python3 << 'PYEOF'
    from pathlib import Path
    import subprocess

    PROJECT_PATH = "<PROJECT_PATH from Step 1>"
    OPERATOR_NAME = "<OPERATOR_NAME from Step 1>"
    PROJECT_NAME = Path(PROJECT_PATH).name

    try:
        REPO_URL = subprocess.check_output(["git", "config", "--get", "remote.origin.url"], stderr=subprocess.DEVNULL).decode().strip()
    except subprocess.CalledProcessError:
        REPO_URL = "(no remote configured)"

    substitutions = {
        "{{PROJECT_PATH}}": PROJECT_PATH,
        "{{PROJECT_NAME}}": PROJECT_NAME,
        "{{REPO_URL}}": REPO_URL,
        "{{OPERATOR_NAME_OR_HANDLE}}": OPERATOR_NAME,
        "{{ONE_PARAGRAPH_PROJECT_DESCRIPTION}}": "(adopter: fill in a one-paragraph description of this project)",
        "{{WORKING_LANGUAGE_NOTE}}": "(adopter: state the working language convention for this project)",
        "{{PROJECT_SPECIFIC_OBLIGATIONS_OR_EMPTY}}": "",
        "{{PROJECT_DESTRUCTIVE_OPS_OR_EMPTY}}": "",
        "{{ADDITIONAL_SIGNALS_OR_EMPTY}}": "",
        "{{ADDITIONAL_STATE_RECORDS_OR_EMPTY}}": "",
        "{{PROJECT_INSTRUCTIONS_CONTEXT_BLOCK}}": (
            "Project context (this Claude.ai Project specifically):\n"
            f"Working repository: {PROJECT_PATH}\n"
            "Pre-flight reads from this path. Do not scan other mounted directories."
        ),
    }

    targets = [
        (".manifesto/mode-2-solo/CLAUDE.md.template", "CLAUDE.md"),
        (".manifesto/mode-2-solo/.claude/instructions.md.template", ".claude/instructions.md"),
    ]

    for src, dst in targets:
        content = Path(src).read_text(encoding='utf-8')
        for placeholder, value in substitutions.items():
            content = content.replace(placeholder, value)
        Path(dst).parent.mkdir(parents=True, exist_ok=True)
        if Path(dst).exists():
            print(f"\033[33m!\033[0m {dst} already existed — overwritten with fresh template")
        Path(dst).write_text(content, encoding='utf-8')
        print(f"\033[32m✓\033[0m wrote {dst}")

    for d in [".claude/sessions", ".claude/inbox", ".claude/state"]:
        Path(d).mkdir(parents=True, exist_ok=True)
        Path(f"{d}/.gitkeep").touch()
        print(f"\033[32m✓\033[0m created {d}/")
    PYEOF

    test -e BACKLOG.md || { touch BACKLOG.md; echo -e "\033[32m✓\033[0m created BACKLOG.md (empty placeholder)"; }
    test -e PROJECT.md || { touch PROJECT.md; echo -e "\033[32m✓\033[0m created PROJECT.md (empty placeholder)"; }


### Step 4 — common (both modes)

Note: substitution of double-curly-brace placeholders in claude-desktop-prefs.md happens automatically through the Python substitutions dict above. All occurrences of each placeholder are replaced in one pass.

The output written to project root claude-desktop-prefs.md contains the usage header (copy this block into Claude.ai Settings) plus the substituted Layer 1 preferences block. Nothing else — no maintainer headers, no design rationale.

    echo -e "\033[32m✓\033[0m Step 4 complete — template files installed"

Step 4 done.


## Step 5 — Hand-off summary

IMPORTANT: Execute the Python snippet below exactly as written. Do NOT paraphrase,
summarize, or replace with your own summary text. The snippet must run as code
and produce its output directly. Do not describe what it does — run it.

    python3 << 'PYEOF'
    import os
    PROJECT_PATH = "<PROJECT_PATH from Step 1>"
    SELECTED_MODE = "<SELECTED_MODE from Step 2>"

    print(f"\033[32m✓\033[0m manifesto v0.7.3 installed in {PROJECT_PATH}")
    print(f"   Mode: {SELECTED_MODE}")
    print()
    print("Done automatically:")
    print(f"  \033[32m✓\033[0m CLAUDE.md")
    print(f"  \033[32m✓\033[0m .claude/instructions.md")
    print(f"  \033[32m✓\033[0m .claude/sessions/  .claude/inbox/  .claude/state/")
    print(f"  \033[32m✓\033[0m BACKLOG.md  PROJECT.md")
    if SELECTED_MODE == "mode-1-triad":
        print(f"  \033[32m✓\033[0m MANIFESTO.md  claude-desktop-prefs.md")
    print()
    print("Your turn:")
    print()
    print("  1) Commit when ready:")
    print(f"       git add -A")
    print(f'       git commit -m "chore(manifesto): install v0.7.3 in {SELECTED_MODE}"')
    print()
    if SELECTED_MODE == "mode-1-triad":
        print("  After Step 6 completes:")
        print()
        print("  2) Open Claude Desktop → left sidebar → Projects →")
        print("     select or create the Project for this repository →")
        print("     Instructions field → paste entire content of:")
        print(f"       \033[36m{PROJECT_PATH}/claude-desktop-prefs.md\033[0m")
        print("     → Save → Restart Claude Desktop")
        print()
        print("  3) Open a new Architect chat inside that Project.")
    else:
        print('  Say "continue" or "what\'s next" to Claude Code to start working.')
    PYEOF

After the Python snippet runs, proceed directly to commit and cleanup. Do not
pause for confirmation — the consent was given at the start of the install.

Execute the install commit:

    git add -A
    git commit -m "chore(manifesto): install v0.7.3 in $SELECTED_MODE mode"

Print:
    echo -e "\033[32m✓\033[0m committed: chore(manifesto): install v0.7.3 in $SELECTED_MODE mode"

Then remove the installer files from the project — they are distribution
artifacts and are not needed after install:

    rm -f setup.md LICENSE
    rm -rf .manifesto/
    git add -A
    git commit -m "chore: remove manifesto installer after setup"
    echo -e "\033[32m✓\033[0m installer files removed and committed"

Then continue: if mode-2-solo — stop and wait for next instruction.
If mode-1-triad — proceed to Step 6.


## Step 6 — MCP filesystem helper (mode-1-triad only)

Skip entirely if SELECTED_MODE is mode-2-solo.

This phase prepares MCP filesystem for the Architect chat in Claude Desktop. Mode-1-triad uses Claude Desktop as the Architect interface, which reads project files via MCP. Without MCP, Architect falls back to Operator pasting file contents manually.

When Step 6 starts, print:

    echo -e "\033[36m→\033[0m Step 6: configuring MCP filesystem for mode-1-triad"

### Step 6.1 — Detect platform and locate Claude Desktop config

Use Python to detect platform and resolve CONFIG_PATH automatically:

    python3 << 'PYEOF'
    import subprocess, sys
    from pathlib import Path

    uname = subprocess.run(["uname", "-s"], capture_output=True, text=True).stdout.strip()
    proc_version = Path("/proc/version").read_text().lower() if Path("/proc/version").exists() else ""

    if "microsoft" in proc_version:
        PLATFORM = "wsl2"
    elif uname == "Darwin":
        PLATFORM = "macos"
    elif uname == "Linux":
        PLATFORM = "linux"
    else:
        PLATFORM = "windows-native"

    if PLATFORM == "wsl2":
        try:
            win_username = subprocess.run(
                ["cmd.exe", "/c", "echo %USERNAME%"],
                capture_output=True, text=True
            ).stdout.strip()
            CONFIG_PATH = f"/mnt/c/Users/{win_username}/AppData/Roaming/Claude/claude_desktop_config.json"
        except Exception:
            CONFIG_PATH = ""
    elif PLATFORM == "macos":
        CONFIG_PATH = str(Path.home() / "Library/Application Support/Claude/claude_desktop_config.json")
    elif PLATFORM == "linux":
        CONFIG_PATH = str(Path.home() / ".config/claude-desktop/claude_desktop_config.json")
    else:
        import os
        appdata = os.environ.get("APPDATA", "")
        CONFIG_PATH = str(Path(appdata) / "Claude/claude_desktop_config.json") if appdata else ""

    print(f"PLATFORM={PLATFORM}")
    print(f"CONFIG_PATH={CONFIG_PATH}")

    if CONFIG_PATH and Path(CONFIG_PATH).exists():
        print("CONFIG_FOUND=yes")
    else:
        print("CONFIG_FOUND=no")
    PYEOF

Parse the output to set PLATFORM, CONFIG_PATH, CONFIG_FOUND.

If CONFIG_FOUND=yes:
    print:
    echo -e "\033[32m✓\033[0m Claude Desktop config found at $CONFIG_PATH"
    Continue to Step 6.2.

If CONFIG_FOUND=no:
    Claude Desktop may not be installed, or config not yet created (first launch needed).
    Print:
    echo -e "\033[33m!\033[0m Claude Desktop config not found."
    echo -e "   If Claude Desktop is not installed: https://claude.ai/download"
    echo -e "   If installed: launch it once to create the config, then re-run setup.md"
    echo -e "\033[32m✓\033[0m All other install steps completed successfully."
    Halt Step 6 here. Do NOT ask the user for a path.


### Step 6.2 — Verify Node.js available

    node --version 2>/dev/null

If exits with non-zero (Node.js missing): try to auto-install based on platform.

Ask Operator:

    \033[33m?\033[0m Node.js not found. Try to install via [detected package manager]? [y/N]

For PLATFORM = "macos": offer "brew install node"
For PLATFORM = "linux" or "wsl2": offer "sudo apt install nodejs npm" (or detect distro and use appropriate command)
For PLATFORM = "windows-native": no auto-install available; print manual instructions

If Y: execute install command. If it succeeds, continue.

    echo -e "\033[36m→\033[0m installing Node.js"
    [run install command]
    node --version && echo -e "\033[32m✓\033[0m Node.js installed: $(node --version)" || { echo -e "\033[31m✗\033[0m auto-install failed; manual install needed"; exit 1; }

If install fails (no sudo, no brew, etc.), print manual command for Operator to run themselves, then halt:

    \033[31m!\033[0m Auto-install failed. Run this command yourself:
       [exact command for this platform]
    \033[31m!\033[0m After Node.js is installed, re-run setup.md to complete Step 6.

If N: print manual install command for the detected platform, halt.


### Step 6.3a — Pre-check and backup

Before writing to claude_desktop_config.json, check whether PROJECT_PATH is
already registered. Use Python to read the config and check:

    python3 << 'PYEOF'
    import json, sys
    from pathlib import Path

    CONFIG_PATH = "<from Step 6.1>"
    PROJECT_PATH = "<from Step 1>"
    PLATFORM = "<from Step 6.1>"

    if PLATFORM == "wsl2":
        EXPECTED_CMD = "wsl.exe"
        EXPECTED_ARGS_PREFIX = ["-e", "npx", "-y", "@modelcontextprotocol/server-filesystem"]
    else:
        EXPECTED_CMD = "npx"
        EXPECTED_ARGS_PREFIX = ["-y", "@modelcontextprotocol/server-filesystem"]

    if not Path(CONFIG_PATH).exists():
        print("NEEDS_WRITE")
        sys.exit(0)

    try:
        existing = json.loads(Path(CONFIG_PATH).read_text())
    except Exception:
        print("NEEDS_WRITE")
        sys.exit(0)

    fs = existing.get("mcpServers", {}).get("filesystem", {})
    current_args = fs.get("args", [])

    if PROJECT_PATH in current_args and fs.get("command") == EXPECTED_CMD:
        print("ALREADY_CONFIGURED")
    else:
        print("NEEDS_WRITE")
    PYEOF

If output is "ALREADY_CONFIGURED":
    The project path is already present in claude_desktop_config.json filesystem
    entry. No write needed, no backup needed, no Operator prompt needed.
    Print confirmation and skip Step 6.3a and Step 6.3 entirely:

    echo -e "\033[32m✓\033[0m claude_desktop_config.json already contains this project path — skipping write"

    Proceed directly to Step 6.4.

If output is "NEEDS_WRITE":
    Show notification and ask consent before writing:

    Print:
    \033[33m!\033[0m MCP filesystem entry not found for this project.
       Will add: $PROJECT_PATH to claude_desktop_config.json
       A backup will be created before any changes.
       Proceed? [y/N]

    Wait for Operator reply. Parse:
    - y or yes: create backup, then proceed to Step 6.3
    - anything else: halt Step 6. Print:

        \033[33m!\033[0m MCP not configured. You can set it up later by re-running setup.md.
        \033[32m✓\033[0m All other install steps completed successfully.

    On affirmative: create timestamped backup:

        BACKUP_PATH="$CONFIG_PATH.backup-$(date +%Y-%m-%d-%H%M%S)"
        cp "$CONFIG_PATH" "$BACKUP_PATH" 2>/dev/null || true
        echo -e "\033[32m✓\033[0m backup created"

    If cp fails: halt Step 6. Print:
        \033[31m✗\033[0m Backup failed. Step 6 aborted — config not modified.

Step 6.3a done. Proceed to Step 6.3.


### Step 6.3 — Generate and write MCP config

For PLATFORM = "wsl2", the config entry uses wsl.exe wrapper:

    {
      "filesystem": {
        "command": "wsl.exe",
        "args": ["-e", "npx", "-y", "@modelcontextprotocol/server-filesystem", "<PROJECT_PATH>"]
      }
    }

For PLATFORM = "macos", "linux", or "windows-native":

    {
      "filesystem": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "<PROJECT_PATH>"]
      }
    }

If CONFIG_PATH does not exist: write fresh config file with the snippet wrapped in {"mcpServers": {...}}.

If CONFIG_PATH exists: read existing JSON, merge in the filesystem entry under mcpServers, write back. Claude Code can execute this directly via Python:

    python3 << 'PYEOF'
    import json, sys
    from pathlib import Path

    CONFIG_PATH = "<from Step 6.1>"
    PROJECT_PATH = "<from Step 1>"
    PLATFORM = "<from Step 6.1>"

    if PLATFORM == "wsl2":
        EXPECTED_CMD = "wsl.exe"
        EXPECTED_ARGS_PREFIX = ["-e", "npx", "-y", "@modelcontextprotocol/server-filesystem"]
    else:
        EXPECTED_CMD = "npx"
        EXPECTED_ARGS_PREFIX = ["-y", "@modelcontextprotocol/server-filesystem"]

    EXPECTED_ARGS = EXPECTED_ARGS_PREFIX + [PROJECT_PATH]

    Path(CONFIG_PATH).parent.mkdir(parents=True, exist_ok=True)
    if Path(CONFIG_PATH).exists():
        existing = json.loads(Path(CONFIG_PATH).read_text())
    else:
        existing = {}
    existing.setdefault("mcpServers", {})

    fs = existing["mcpServers"].get("filesystem")

    if fs is None:
        # Case A: no filesystem entry — fresh write
        existing["mcpServers"]["filesystem"] = {"command": EXPECTED_CMD, "args": EXPECTED_ARGS}
        Path(CONFIG_PATH).write_text(json.dumps(existing, indent=2))
        print(f"\033[32m✓\033[0m wrote {CONFIG_PATH} (new filesystem entry)")
    elif fs.get("command") == EXPECTED_CMD:
        # Case B: command matches — merge PROJECT_PATH into existing args
        current_args = fs.get("args", [])
        if PROJECT_PATH in current_args:
            print(f"\033[32m✓\033[0m {CONFIG_PATH} already contains {PROJECT_PATH} — no change needed")
        else:
            prev_paths = [a for a in current_args if a not in EXPECTED_ARGS_PREFIX]
            fs["args"] = current_args + [PROJECT_PATH]
            existing["mcpServers"]["filesystem"] = fs
            Path(CONFIG_PATH).write_text(json.dumps(existing, indent=2))
            print(f"\033[32m✓\033[0m merged into {CONFIG_PATH}")
            print(f"  existing paths preserved: {len(prev_paths)}")
            print(f"  added: {PROJECT_PATH}")
    else:
        # Case C: command mismatch — show diff, ask Operator
        print(f"\033[33m?\033[0m Current filesystem entry uses a different command structure.")
        print(f"  Current:  command={fs.get('command')!r}  args={fs.get('args')}")
        print(f"  Intended: command={EXPECTED_CMD!r}  args={EXPECTED_ARGS}")
        print()
        print("  Reply:")
        print("    1   REPLACE   overwrite current entry (existing paths will be lost)")
        print("    2   SKIP      leave config unchanged; configure MCP manually later")
        while True:
            reply = input("Choice [1/2]: ").strip()
            if reply == "1":
                existing["mcpServers"]["filesystem"] = {"command": EXPECTED_CMD, "args": EXPECTED_ARGS}
                Path(CONFIG_PATH).write_text(json.dumps(existing, indent=2))
                print(f"\033[32m✓\033[0m wrote {CONFIG_PATH} (replaced existing entry)")
                break
            elif reply == "2":
                print(f"\033[33m!\033[0m Step 6.3 skipped — claude_desktop_config.json unchanged.")
                print(f"  To configure MCP manually, add to {CONFIG_PATH}:")
                snippet = {"command": EXPECTED_CMD, "args": EXPECTED_ARGS}
                print(f'  "filesystem": {json.dumps(snippet, indent=4)}')
                sys.exit(0)
            else:
                print("  Please reply 1 or 2.")
PYEOF


### Step 6.4 — Restart instructions

This step Operator must do manually (Claude Code cannot restart Claude Desktop).

Use Python to print restart instructions so PROJECT_PATH interpolates correctly:

    python3 << 'PYEOF'
    PROJECT_PATH = "<PROJECT_PATH from Step 1>"

    print("\033[33m!\033[0m Manual steps to complete setup — do them in this order:")
    print()
    print("  1) Open Claude Desktop → left sidebar → Projects →")
    print("     select or create the Project for this repository →")
    print("     Instructions field → paste the entire content of:")
    print(f"       \033[36m{PROJECT_PATH}/claude-desktop-prefs.md\033[0m")
    print("     → click Save")
    print()
    print("  2) Fully quit Claude Desktop")
    print("     (system tray → Quit, or app menu → Quit — closing the window is not enough;")
    print("     Claude Desktop only reloads MCP config on full process restart)")
    print()
    print("  3) Relaunch Claude Desktop")
    print("  4) In Settings → Developer, verify 'filesystem' shows as connected")
    print()
    print("  5) Open Architect chat:")
    print("     Claude Desktop → left sidebar → Projects →")
    print(f"     select the Project for this repository → New chat")
    print()
    print("\033[32m✓\033[0m Step 6 done — MCP and Architect configuration prepared.")
    PYEOF

Stop. Wait for next Operator instruction.


## Edge cases and recovery

If anything fails mid-phase, do not silently continue. Print the failure with red marker, explain what state the project is in, and what manual recovery is possible.

To undo a failed install, remove the manifesto files that were added:

    rm -f CLAUDE.md MANIFESTO.md BACKLOG.md PROJECT.md claude-desktop-prefs.md
    rm -rf .claude/

Your existing project files are untouched — only the manifesto files added during
install need to be removed.

If placeholder substitution leaves double-curly-brace strings visible in installed files, those placeholders were not in the substitutions dictionary. Print which ones remain unsubstituted. Operator fills them manually or extends the dict.



## What this file is not

This file does not configure Claude.ai Settings, Claude Code settings, git remotes, or CI/CD. Those are Operator's responsibility before or after setup.

Step 6 prepares the MCP config snippet and writes it to claude_desktop_config.json for the adopter. It does not restart Claude Desktop (Claude Code cannot control that process) and it does not paste into Claude.ai Settings UI (a manual paste step is needed).

This file does not enforce manifesto discipline at runtime. The rules in Layer 1 (claude-desktop-prefs.md) and Layer 2 (.claude/instructions.md) do that work after setup completes. Setup just bootstraps the framework.

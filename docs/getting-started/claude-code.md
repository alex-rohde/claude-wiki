# Claude Code (CLI)

Claude Code runs in your terminal. It can read your project files, make edits, run shell commands, and work through multi-step coding tasks autonomously, all within your actual project directory.

## Requirements

- macOS 13.0 or later
- 4 GB+ RAM
- Internet connection
- Paid Claude account (Pro, Max, Team, or Enterprise)

## Installation

Open Terminal and run:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

This installs the `claude` binary to `~/.local/bin` and sets up automatic background updates.

??? note "Alternative: Homebrew"
    If you prefer to manage updates yourself via Homebrew:
    ```bash
    brew install --cask claude-code
    ```
    Homebrew installs do **not** auto-update. Run `brew upgrade claude-code` to get new versions.

---

## If the install script doesn't work

If the `curl` command above fails or the `claude` command isn't found after running it, install via Homebrew instead.

### Step 1 — Install Homebrew

Homebrew is a package manager for macOS. If you don't have it, run this in Terminal:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the prompts — it may ask for your Mac password and install Xcode Command Line Tools if needed. When it finishes, verify it worked:

```bash
brew --version
```

### Step 2 — Install Claude Code

```bash
brew install --cask claude-code
```

### Step 3 — Verify

```bash
claude --version
```

!!! note
    Homebrew installs do **not** auto-update. To get new versions run `brew upgrade claude-code`, or run `claude update` from inside a session to be prompted with the upgrade command.

### Verify the installation

```bash
claude --version
```

For a full health check:

```bash
claude doctor
```

## Authentication

Run `claude` — it will open a browser window to log in with your Claude account:

```bash
claude
```

Follow the prompts. You only need to do this once.

## Basic usage

Navigate to your project directory, then start Claude:

```bash
cd ~/your-project
claude
```

Type your task in plain language. Examples:

- `Fix the type error in src/api/users.ts`
- `Write Jest tests for the auth middleware`
- `Explain what this function does`
- `Refactor this module to use async/await`

Claude will show you what it plans to do — file edits, commands to run — and ask for confirmation before anything destructive.

## Common commands

| Command | What it does |
|---|---|
| `claude` | Start an interactive session in the current directory |
| `claude "your task"` | Run a one-shot task without entering interactive mode |
| `claude update` | Manually update to the latest version |
| `claude doctor` | Check your installation and configuration |
| `claude --help` | Show all available flags |

### In-session slash commands

Type these while Claude Code is running:

| Command | What it does |
|---|---|
| `/help` | Show available commands |
| `/clear` | Clear conversation context and start fresh |
| `/exit` | Exit the session |
| `/config` | Open configuration settings |

## IDE integration

Claude Code works inside VS Code and JetBrains IDEs without switching to a separate terminal window.

**VS Code:**

1. Open VS Code.
2. Go to the Extensions panel (`Cmd+Shift+X`).
3. Search for **Claude Code** and install it.
4. Open the Claude Code panel from the sidebar.

**JetBrains (IntelliJ, WebStorm, PyCharm, etc.):**

1. Open Preferences → Plugins.
2. Search for **Claude Code** and install it.
3. Find the Claude Code tool window in the right sidebar.

## Updates

Native installs update automatically in the background. The update takes effect the next time you start Claude Code. To update immediately:

```bash
claude update
```

# GrowthX Buildathon — Laptop Setup

**Do this BEFORE you arrive (about 15 minutes)**

The event is 3 hours of building, not installing. If your laptop is set up the night before, you can start building right away. If you get stuck on any step, come 15 minutes early and a mentor will help you finish.

## Before you start, you'll need

- A laptop (Windows or Mac) where you can install software (admin rights).
- A stable internet connection.
- An AI subscription you can log in to — either:
  - **Claude Pro or Max** (recommended — powers "Claude Code"), OR
  - **ChatGPT Plus/Pro** (powers "Codex")
- About 15 minutes.

## Quick checklist (tick all 7)

- [ ] Node.js installed (version 20 or newer)
- [ ] An IDE installed (VS Code recommended)
- [ ] Your AI tool installed in the IDE terminal — Claude Code (recommended) or Codex
- [ ] Logged in to your AI tool with your subscription
- [ ] Python installed (3.10+) — for the Python build path
- [ ] Git installed — to save your progress
- [ ] Project folder created + everything verified (final step)

## Step 1 — Install Node.js (required)

*Why: your AI tools (Claude Code / Codex) are installed and run through Node.js.*

**Windows**
- Easiest: go to https://nodejs.org → download the **LTS** version → run the installer → keep clicking Next → Finish.
- Or, in a terminal:
```powershell
winget install OpenJS.NodeJS.LTS
```

**macOS**
- Easiest: go to https://nodejs.org → download the macOS **LTS** → run the .pkg installer.
- Or, if you have Homebrew:
```bash
brew install node
```

## Step 2 — Install an IDE (your workspace)

*An IDE is the app where you'll see your files and run commands. It has a built-in "terminal" you'll use in the next step. Pick ONE (Windows & macOS versions on each site):*

- **VS Code** (recommended — free, simple, works everywhere): https://code.visualstudio.com
- **Google Antigravity** (an AI-first IDE): https://antigravity.google
- **Cursor** (an AI-first IDE): https://cursor.com

*Not sure? Choose VS Code. (Antigravity and Cursor are also built on VS Code, so the steps below are the same. They have their own built-in AI, but for this build we'll use Claude Code in the terminal — same for everyone.)*

**Download your chosen IDE, install it, and open it.**

## Step 3 — Open the IDE's terminal and install your AI tool

**Open the built-in terminal inside your IDE:**
- Top menu → Terminal → New Terminal
- …or press **Ctrl + `** on Windows / **Cmd + `** on macOS (the key above Tab).

A terminal panel opens at the bottom of the IDE. Type the command for your tool there:

**Claude Code (recommended):**
```bash
npm install -g @anthropic-ai/claude-code
```

**Codex (alternative):**
```bash
npm install -g @openai/codex
```

*Tip: if it says "npm is not recognized", close the IDE and open it again after installing Node (Step 1), then reopen the terminal.*

## Step 4 — Log in to your AI tool

In the same IDE terminal, log in.

**Claude Code:**
```bash
claude
```
- A browser window opens — sign in with your Claude account. When it's done, type `/exit` to leave.

**Codex:**
```bash
codex login
```

## Step 5 — Install Python (for the Python path)

*Why: the default build uses Python. (If you plan to build in JavaScript instead, Node from Step 1 is enough — you can skip this step.)*

**Windows**
- Go to https://www.python.org/downloads/ → Download Python.
- Run the installer. **IMPORTANT: tick "Add Python to PATH"** at the bottom, then click Install Now.

**macOS**
- Go to https://www.python.org/downloads/ → download the macOS installer → run it.
- Or, with Homebrew:
```bash
brew install python
```

## Step 6 — Install Git (to save your progress)

*Why: during the build you'll "checkpoint" your work so you never lose it. Git makes that possible.*

**Windows**
- Go to https://git-scm.com/download/win → download → run the installer → accept the defaults.
- Or, in a terminal:
```powershell
winget install Git.Git
```

**macOS**
- In the terminal, type the line below. If Git isn't installed, macOS pops up an "Install" button — click it.
```bash
git --version
```
- Or, with Homebrew:
```bash
brew install git
```

## Step 7 — Create your project folder

Make an empty folder where your agent will live, then open it inside your IDE.

**Windows (IDE terminal)**
```powershell
mkdir "$HOME\my-twin"
```

**macOS (IDE terminal)**
```bash
mkdir ~/my-twin
```

- In your IDE: **File → Open Folder →** choose your `my-twin` folder.
- Download the two starter files — `IDEATION.md` and `BUILDING.md` (the organizers will share the link) — and move them into the `my-twin` folder.

## Step 8 — Verify everything works

In your IDE terminal, run these. Each should print a version (or a reply) — not an error.

**Windows**
```powershell
node --version
python --version
git --version
claude --version
```

**macOS**
```bash
node --version
python3 --version
git --version
claude --version
```

*(Codex users: use `codex --version` instead of `claude --version`.)*

**Final test — check your AI tool can actually answer:**

Claude Code:
```bash
claude -p "say hello in one word"
```

Codex:
```bash
echo "say hello in one word" | codex exec -
```

**If you get a one-word reply back, you are 100% ready for the build.**

## If something goes wrong

- "...is not recognized" / "command not found" → close your IDE and open it again (so the terminal picks up the new install), then try again.
- On Windows, if Python is still not found → reinstall it and make sure you tick "Add Python to PATH".
- Login problems → run `claude` (or `codex login`) again and finish the sign-in in your browser.
- Still stuck? Come 15 minutes early on event day — a mentor will get you set up quickly.

# ⚠️ Before You Arrive — Do This the Night Before

> The event is **3 hours of building, not installing.** Show up with this done (~20–30 min). Stuck? Come early — a volunteer will help.

> 🐍🟨 **Pick your path** with the **Python / JavaScript** toggle at the top of this handbook. The steps below switch to match. **Node.js is needed either way** (your AI tool runs on it); **Python is only for the Python path.**

## ✅ The Checklist (tick all 6)

- [ ] **Node.js** installed (20+) — *both paths*
- [ ] **Python** installed (3.10+) — *Python path only*
- [ ] **AI tool** installed — **Claude Code** (recommended) or **Codex**
- [ ] **Logged in** with your Pro subscription
- [ ] **Project folder** created
- [ ] **Verified** it works (final command below)

Now follow your operating system.

## 🪟 Windows

1. **Node.js** — [nodejs.org](https://nodejs.org) → download **LTS** → install. *(both paths)*
2. **Python** *(Python path only — skip for JavaScript)* — [python.org/downloads](https://www.python.org/downloads/) → Download → run installer → **tick "Add Python to PATH"** → Install.
3. **Install your AI tool** — open **PowerShell** and run the block for your tool:

🟣 **Claude users — RUN** (PowerShell)
```powershell
npm install -g @anthropic-ai/claude-code
```

🟢 **Codex users — RUN** (PowerShell)
```powershell
npm install -g @openai/codex
```

4. **Log in** — open the tool once and sign in with your Pro account:

🟣 **Claude users — RUN** (then sign in, type `/exit`)
```powershell
claude
```

🟢 **Codex users — RUN**
```powershell
codex login
```

> 💡 If you see **"pip is not recognized"**, use `python -m pip` instead of `pip`.

## 🍎 macOS

1. **Homebrew** (if you don't have it) — in **Terminal**:

💻 **RUN**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. **Install the runtime** (Node for both; Python for the Python path):

:::py
💻 **RUN** (Python path)
```bash
brew install node python
```
:::
:::js
💻 **RUN** (JavaScript path)
```bash
brew install node
```
:::

3. **Install your AI tool:**

🟣 **Claude users — RUN**
```bash
npm install -g @anthropic-ai/claude-code
```

🟢 **Codex users — RUN**
```bash
npm install -g @openai/codex
```

4. **Log in:**

🟣 **Claude users — RUN** (then sign in, type `/exit`)
```bash
claude
```

🟢 **Codex users — RUN**
```bash
codex login
```

## 🐧 Linux (Ubuntu/Debian)

1. **Install the runtime:**

:::py
💻 **RUN** (Python path)
```bash
sudo apt update
sudo apt install -y python3 python3-pip nodejs npm
```
:::
:::js
💻 **RUN** (JavaScript path)
```bash
sudo apt update
sudo apt install -y nodejs npm
```
:::

2. **Install your AI tool:**

🟣 **Claude users — RUN**
```bash
npm install -g @anthropic-ai/claude-code
```

🟢 **Codex users — RUN**
```bash
npm install -g @openai/codex
```

3. **Log in:**

🟣 **Claude users — RUN** (then sign in, type `/exit`)
```bash
claude
```

🟢 **Codex users — RUN**
```bash
codex login
```

> Fedora/RHEL: `sudo dnf install nodejs npm` (add `python3 python3-pip` for the Python path).

## 📁 Create your project folder

🪟 **Windows (PowerShell) — RUN**
```powershell
mkdir "$HOME\my-twin"
```

🍎🐧 **macOS / Linux — RUN**
```bash
mkdir ~/my-twin
```

Then drop the two **starter files** into it — **`IDEATION.md`** and **`BUILDING.md`** (download them from Phase 1 of this handbook).

## ✅ Final check — does it all work?

Run inside your project folder. Each should print a version, not an error.

:::py
💻 **RUN** (Python path)
```bash
python --version
node --version
```
:::
:::js
💻 **RUN** (JavaScript path)
```bash
node --version
```
:::

Then confirm your AI tool can talk — run the block for your tool:

🟣 **Claude users — RUN**
```bash
claude -p "say hello in one word"
```

🟢 **Codex users — RUN**
```bash
echo "say hello in one word" | codex exec -
```

> ✅ Got a one-word reply? **You're 100% ready.** 🎉

> 🔋 **One setting that saves your session:** in Claude Code type **`/model sonnet`**. Sonnet is fast and has much higher Pro limits than Opus — plenty for this build.

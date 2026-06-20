# How This Handbook Works (3-minute read)

**You don't write code — your AI tool does.** You copy prompts, paste them in, run a command to test. The loop:

1. Copy a **📋 PROMPT** from the handbook.
2. Paste it into **Claude Code / Codex** → it writes the code.
3. Run the **💻 RUN** command to test.
4. ✅ Works? Checkpoint, then next step.

## The boxes you'll see

- **📋 PROMPT** — paste the whole thing into your AI tool.
- **💻 RUN** — type/paste this in your terminal.
- **✅ SUCCESS LOOKS LIKE** — what you should see when it worked.
- **🛟 fallback code** — copy this if your AI is slow or out of quota. You're never blocked.
- **🟣 Claude / 🟢 Codex** — most steps are identical; follow your tool's box when they differ.
- **🐍 Python / 🟨 JavaScript** — pick your path with the toggle at the top of the page; every command and code block switches to match.

## Start your AI tool

1. Open a terminal in your `my-twin` folder.
2. 🟣 Claude: run `claude`. 🟢 Codex: run `codex`.
3. Paste prompts at its input. When a prompt says "create core.py…", it makes the file — just review and approve.

## 🔋 Don't run out of session — 4 rules

1. **Use Sonnet** — type `/model sonnet`. Fast, high limits.
2. **One prompt per step** — no long back-and-forth (each round costs quota).
3. **Hit a limit? Use the 🛟 code** — copying it costs zero quota.
4. **Test with 2–3 messages, not 20** — each twin reply costs quota too.

> 🔋 Building on Sonnet is light. The quota hogs are long chats and over-testing. Stay frugal and you'll finish easily.

## Checkpoints = your safety net

After each working step, paste a prompt like *"commit my work with message X"* — your AI saves it with git. If something breaks, you roll back to the last checkpoint. Don't skip them.

➡️ **Next: Phase 1 — Ideation.**

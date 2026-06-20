# 🆘 Troubleshooting — Fix Almost Anything

Find your symptom → follow the fix. 90% of problems are here.

> 🔋 **Escape hatch:** AI slow or out of quota? Every file's full code is in **`09-REFERENCE-CODE.md`** — copy it by hand, zero quota, never blocked.

---

:::py
## 🐍 Python & pip

**`'python' is not recognized` / `command not found: python`**
1. Try `python3` instead of `python`.
2. Still failing? Re-run the Python installer → **Modify** → tick **"Add Python to PATH"** → reopen terminal.

**`'pip' is not recognized` (Windows)**
1. Use `python -m pip` instead of `pip` (e.g. `python -m pip install -r requirements.txt`).
:::

:::js
## 🟨 Node & npm (JavaScript)

**`'node' is not recognized` / `command not found: node`**
1. Install Node 20+ from [nodejs.org](https://nodejs.org), then **reopen your terminal**.
2. Check it: `node --version` (need v20+).

**`Error: Cannot find module '...'`**
1. Dependencies aren't installed. In your project folder run `npm install` (or `npm install <package>` for the one it names).

**`SyntaxError ... in JSON` when it starts**
1. A data file (`memory.json` / `notes.json` / `chats.json`) got corrupted. Delete it — your twin recreates it on the next run.
:::

**Can't find `PERSONALITY.md` / "no such file"**
1. You're in the wrong folder. `cd` into your project first, then run your twin:

:::py
```bash
cd path/to/my-twin
python core.py "hi"
```
:::
:::js
```bash
cd path/to/my-twin
node core.js "hi"
```
:::

---

## 🤖 Your AI tool (Claude / Codex)

**`'claude' is not recognized` / `command not found` (same for `codex`)**
1. Check Node: `node --version` (need v20+).
2. Reinstall: `npm install -g @anthropic-ai/claude-code` (or `@openai/codex`).
3. **Close and reopen your terminal.**

**Asks you to log in / auth error**
- 🟣 Claude: run `claude` once, sign in with Pro, then `/exit`.
- 🟢 Codex: run `codex login`.

**"usage limit reached" / replies stop**
1. Switch model: type **`/model sonnet`**.
2. Use the **🛟 reference code** in `09-REFERENCE-CODE.md` (zero quota).
3. Keep test messages short and few.
4. Limits refill over time — take a short break.

---

## 💬 The twin (your core file)

:::py
**`UnicodeEncodeError` on emojis (Windows, Python)**
1. Confirm the top of `core.py` has:
```python
import sys
sys.stdout.reconfigure(encoding="utf-8")
```
:::

**Replies, but doesn't sound like you**
1. Edit `PERSONALITY.md` (slang, length, emojis, greetings). It's read fresh every reply — no restart. (See Phase 5.)

**`[error from claude CLI: ...]` instead of a reply**
1. The AI tool failed — usually login or quota. Run `claude -p "hi"` to see the real error, then fix login/quota above.

---

## 🌐 Web UI (the server)

**`Address already in use` (port 8000 busy)**
1. Close the other copy, or open your server file (`server.py` / `server.js`), change `PORT = 8000` to `8001`, and use `http://localhost:8001`.

**Blank page / "can't connect"**
1. Confirm your server is still running — `python server.py` *(or `node server.js`)* — and open the exact address it printed.

---

## 📱 Slack

**"Sending messages to this app has been turned off."**
1. api.slack.com/apps → your app → **App Home** → **Show Tabs**.
2. Enable **Messages Tab** AND tick **"Allow users to send Slash commands and messages from the messages tab"**.
3. Reload Slack.

**Bot added but never replies**
1. **Socket Mode** is ON.
2. **Event Subscriptions** → `message.im` + `app_mention` added AND **Save Changes** clicked.
3. **Reinstalled** the app after adding scopes.
4. Both tokens (`xoxb-`, `xapp-`) correct in `slack_tokens.txt`.

---

## ✈️ Telegram

**Bot doesn't reply**
1. Token in `bot_token.txt` matches @BotFather exactly (no spaces).
2. Open the bot and press **Start** / send `/start` once.
3. Your bot is still running — `python telegram_bot.py` *(or `node telegram_bot.js`)*.

---

## 🎮 Discord

**Online but ignores messages**
1. discord.com/developers → your app → **Bot** → Privileged Gateway Intents → turn on **MESSAGE CONTENT INTENT** → Save → restart the bot.

:::py
**`pip install discord.py` fails / `import discord` errors**
1. Install with `python -m pip install discord.py`, then run again.
:::
:::js
**`Cannot find module 'discord.js'`**
1. Run `npm install discord.js` in your project folder (Node 20+), then run again.
:::

---

## 🔐 Safety

**Worried tokens got committed to git**
1. Make sure `.gitignore` lists: `bot_token.txt`, `slack_tokens.txt`, `discord_token.txt`, `memory.json`, `notes.json`, `chats.json`.
2. Ask your AI tool: *"add these files to .gitignore"*.

---

**Still stuck?** Grab a volunteer, or paste the exact error into your AI tool and ask *"how do I fix this?"* 🙂

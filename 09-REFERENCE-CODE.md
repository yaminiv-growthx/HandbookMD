# 🛟 Reference Code — Copy This If You're Stuck

This is the **complete, finished code** for the whole project. If your AI tool is slow or
you've hit a usage limit, you can build the *entire* agent by copying these files yourself —
**no AI quota needed at all.** Create each file in your project folder and paste the code.

> 🐍🟨 **Pick your language with the toggle at the top** — every file below switches between
> Python and JavaScript.

> 🟢 **Codex users:** in `core.py` / `core.js`, change the brain command from
> `claude -p --model sonnet` to `codex exec -`. Everything else is identical.

> 🔋 This is your safety net. You can always generate with prompts *and* fall back to these
> when needed — mix and match freely.

**Files in the finished project:**

:::py
`PERSONALITY.md` · `core.py` · `server.py` · `index.html` · `telegram_bot.py` · `slack_bot.py` · `discord_bot.py` · `requirements.txt`
:::
:::js
`PERSONALITY.md` · `core.js` · `server.js` · `index.html` · `telegram_bot.js` · `slack_bot.js` · `discord_bot.js` · `package.json`
:::

---

## `PERSONALITY.md` — your twin's "soul" (example — edit to be YOU)

```markdown
# Personality Profile — My Digital Twin

## Voice & Language
- Language: Hinglish (Hindi + English in English letters)
- Tone: casual & friendly
- Message length: medium (a couple of sentences)
- Emojis: sometimes, not every line
- Grammatical gender: ALWAYS use feminine Hindi/Hinglish forms ("kar rahi hoon", "gayi")
  [change to masculine if that's you]

## How I Talk
- Greeting: "hey" / "heyy"
- I address people as: yaar, bro/bhai, or by name
- Default energy: warm & caring
- Sign-off: warm close ("take care yaar")

## Personality & Humor
- People should find me interesting and funny
- Humor: playful roasting + dry sarcasm + witty one-liners
- Strong opinions, said in a chill way
- I love talking about: tech & startups, movies/sports, everyday vibing

## Natural Variation (don't sound robotic)
- Vary how replies open — don't start every message the same way
- Vary length and shape; don't use the same emoji every time
- Match the other person's energy and language

## Behavior Rules (NEVER do these)
- No commitments (money, meetings, big yes/no) — say "let me get back on that"
- Never share private info (address, finances)
- Never rude or abusive, even if provoked
- No fake expertise — if unsure say "let me check and tell you"
```

---

## `core.py` / `core.js` — the brain + memory + notes (the heart of everything)

:::py
```python
import sys
import os
import json
import re
import subprocess

# Make sure emojis print correctly in the terminal
sys.stdout.reconfigure(encoding="utf-8")

# Always find files next to this script, wherever you run it from
HERE = os.path.dirname(os.path.abspath(__file__))
PERSONALITY_FILE = os.path.join(HERE, "PERSONALITY.md")
MEMORY_FILE = os.path.join(HERE, "memory.json")
NOTES_FILE = os.path.join(HERE, "notes.json")
CHATS_FILE = os.path.join(HERE, "chats.json")


def load_history():
    if os.path.exists(MEMORY_FILE):
        with open(MEMORY_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    return []


def save_history(history):
    with open(MEMORY_FILE, "w", encoding="utf-8") as f:
        json.dump(history, f, ensure_ascii=False, indent=2)


def load_notes():
    if os.path.exists(NOTES_FILE):
        with open(NOTES_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    return []


def save_notes(notes):
    with open(NOTES_FILE, "w", encoding="utf-8") as f:
        json.dump(notes, f, ensure_ascii=False, indent=2)


def load_chat(chat_id):
    """Per-conversation history for the bots (keyed by chat id), survives restarts."""
    if os.path.exists(CHATS_FILE):
        with open(CHATS_FILE, "r", encoding="utf-8") as f:
            all_chats = json.load(f)
        return all_chats.get(str(chat_id), [])
    return []


def save_chat(chat_id, history):
    all_chats = {}
    if os.path.exists(CHATS_FILE):
        with open(CHATS_FILE, "r", encoding="utf-8") as f:
            all_chats = json.load(f)
    all_chats[str(chat_id)] = history
    with open(CHATS_FILE, "w", encoding="utf-8") as f:
        json.dump(all_chats, f, ensure_ascii=False, indent=2)


def get_reply(their_message, history):
    """Build the prompt (personality + facts + chat so far + new message), ask the AI, return the reply."""
    with open(PERSONALITY_FILE, "r", encoding="utf-8") as f:
        personality = f.read()

    history_text = ""
    for turn in history:
        history_text += turn["role"] + ": " + turn["text"] + "\n"
    if history_text == "":
        history_text = "(no messages yet — this is the start of the conversation)"

    notes = load_notes()
    if notes:
        notes_text = ""
        for n in notes:
            notes_text += "- " + n + "\n"
    else:
        notes_text = "(nothing saved yet)"

    prompt = (
        "You are my digital twin. Reply to the latest message EXACTLY as I would, "
        "matching my voice, humor, and rules in my personality profile. "
        "Use the conversation so far for context so your reply makes sense. "
        "Use the THINGS I KNOW section to recall any facts the person asks about.\n\n"
        "CAPABILITY — saving facts: If the latest message contains a fact worth "
        "remembering long-term (a date, plan, preference, or important detail), then "
        "AFTER your normal reply add a new line in EXACTLY this format:\n"
        "[NOTE: the fact in a short clear sentence]\n"
        "Only do this for genuinely useful facts — not for small talk or jokes. "
        "If there's nothing worth saving, don't add a NOTE line at all.\n"
        "Apart from that NOTE line, output ONLY the reply text — no quotes, no explanation.\n\n"
        "===== MY PERSONALITY PROFILE =====\n"
        + personality +
        "\n===== THINGS I KNOW (saved facts) =====\n"
        + notes_text +
        "\n===== CONVERSATION SO FAR =====\n"
        + history_text +
        "\n===== LATEST MESSAGE TO REPLY TO =====\n"
        + their_message
    )

    # The "brain": call the claude CLI on Sonnet, feeding the prompt via stdin.
    # 🟢 Codex users: change "claude -p --model sonnet" to "codex exec -"
    result = subprocess.run(
        "claude -p --model sonnet",
        input=prompt,
        shell=True,
        capture_output=True,
        text=True,
        encoding="utf-8",
    )
    if result.returncode != 0:
        return "[error from claude CLI: " + result.stderr.strip() + "]"

    reply = result.stdout.strip()

    # Pull out any [NOTE: ...] lines, save the facts, and hide them from the user
    found_notes = re.findall(r"\[NOTE:\s*(.+?)\]", reply)
    if found_notes:
        for fact in found_notes:
            fact = fact.strip()
            if fact and fact not in notes:
                notes.append(fact)
        save_notes(notes)
        reply = re.sub(r"\s*\[NOTE:\s*.+?\]", "", reply).strip()

    return reply


def reset_memory():
    if os.path.exists(MEMORY_FILE):
        os.remove(MEMORY_FILE)


def chat_loop():
    print("\n🧑 Your twin is online. Type a message and press Enter.")
    print("   (type 'exit' to quit, 'reset' to start a fresh conversation)\n")
    history = load_history()
    while True:
        try:
            their_message = input("You: ").strip()
        except (KeyboardInterrupt, EOFError):
            print("\n👋 Chat ended. Memory saved.")
            break
        if their_message == "":
            continue
        if their_message.lower() == "exit":
            print("👋 Chat ended. Memory saved.")
            break
        if their_message.lower() == "reset":
            reset_memory()
            history = []
            print("🧹 Memory cleared. Fresh conversation started.\n")
            continue
        reply = get_reply(their_message, history)
        history.append({"role": "Them", "text": their_message})
        history.append({"role": "Me", "text": reply})
        save_history(history)
        print("Twin:", reply, "\n")


def one_shot(their_message):
    if their_message.lower() == "reset":
        reset_memory()
        print("🧹 Memory cleared. Fresh conversation started.")
        return
    history = load_history()
    reply = get_reply(their_message, history)
    history.append({"role": "Them", "text": their_message})
    history.append({"role": "Me", "text": reply})
    save_history(history)
    print("\nThem:", their_message)
    print("You (twin):", reply, "\n")


if __name__ == "__main__":
    if len(sys.argv) < 2:
        chat_loop()                 # no message -> live chat
    else:
        one_shot(sys.argv[1])       # message -> single reply
```

**Run it:** `python core.py` (live chat) · `python core.py "hi"` (one reply) · `python core.py reset` (clear memory)
:::

:::js
```javascript
const fs = require("fs");
const path = require("path");
const { execSync } = require("child_process");
const readline = require("readline");

// Always find files next to this script, wherever you run it from
const HERE = __dirname;
const PERSONALITY_FILE = path.join(HERE, "PERSONALITY.md");
const MEMORY_FILE = path.join(HERE, "memory.json");
const NOTES_FILE = path.join(HERE, "notes.json");
const CHATS_FILE = path.join(HERE, "chats.json");

// small JSON helpers
function loadJSON(file, fallback) {
  if (fs.existsSync(file)) return JSON.parse(fs.readFileSync(file, "utf8"));
  return fallback;
}
function saveJSON(file, data) {
  fs.writeFileSync(file, JSON.stringify(data, null, 2));
}

function loadHistory() { return loadJSON(MEMORY_FILE, []); }
function saveHistory(h) { saveJSON(MEMORY_FILE, h); }
function loadNotes() { return loadJSON(NOTES_FILE, []); }
function saveNotes(n) { saveJSON(NOTES_FILE, n); }

// Per-conversation history for the bots (keyed by chat id), survives restarts
function loadChat(chatId) {
  const all = loadJSON(CHATS_FILE, {});
  return all[String(chatId)] || [];
}
function saveChat(chatId, history) {
  const all = loadJSON(CHATS_FILE, {});
  all[String(chatId)] = history;
  saveJSON(CHATS_FILE, all);
}

function getReply(theirMessage, history) {
  const personality = fs.readFileSync(PERSONALITY_FILE, "utf8");

  let historyText = history.map(t => `${t.role}: ${t.text}`).join("\n");
  if (!historyText) historyText = "(no messages yet — this is the start of the conversation)";

  let notes = loadNotes();
  const notesText = notes.length ? notes.map(n => "- " + n).join("\n") : "(nothing saved yet)";

  const prompt =
    "You are my digital twin. Reply to the latest message EXACTLY as I would, " +
    "matching my voice, humor, and rules in my personality profile. " +
    "Use the conversation so far for context so your reply makes sense. " +
    "Use the THINGS I KNOW section to recall any facts the person asks about.\n\n" +
    "CAPABILITY — saving facts: If the latest message contains a fact worth " +
    "remembering long-term (a date, plan, preference, or important detail), then " +
    "AFTER your normal reply add a new line in EXACTLY this format:\n" +
    "[NOTE: the fact in a short clear sentence]\n" +
    "Only do this for genuinely useful facts — not for small talk or jokes. " +
    "If there's nothing worth saving, don't add a NOTE line at all.\n" +
    "Apart from that NOTE line, output ONLY the reply text — no quotes, no explanation.\n\n" +
    "===== MY PERSONALITY PROFILE =====\n" + personality +
    "\n===== THINGS I KNOW (saved facts) =====\n" + notesText +
    "\n===== CONVERSATION SO FAR =====\n" + historyText +
    "\n===== LATEST MESSAGE TO REPLY TO =====\n" + theirMessage;

  // The "brain": call the claude CLI on Sonnet, feeding the prompt via stdin.
  // 🟢 Codex users: change "claude -p --model sonnet" to "codex exec -"
  let reply;
  try {
    reply = execSync("claude -p --model sonnet", { input: prompt, encoding: "utf8" }).trim();
  } catch (e) {
    return "[error from claude CLI: " + (e.stderr ? e.stderr.toString().trim() : e.message) + "]";
  }

  // Pull out any [NOTE: ...] lines, save the facts, and hide them from the user
  const found = [...reply.matchAll(/\[NOTE:\s*(.+?)\]/g)].map(m => m[1].trim());
  if (found.length) {
    for (const fact of found) if (fact && !notes.includes(fact)) notes.push(fact);
    saveNotes(notes);
    reply = reply.replace(/\s*\[NOTE:\s*.+?\]/g, "").trim();
  }
  return reply;
}

function resetMemory() {
  if (fs.existsSync(MEMORY_FILE)) fs.unlinkSync(MEMORY_FILE);
}

function chatLoop() {
  console.log("\n🧑 Your twin is online. Type a message and press Enter.");
  console.log("   (type 'exit' to quit, 'reset' to start a fresh conversation)\n");
  let history = loadHistory();
  const rl = readline.createInterface({ input: process.stdin, output: process.stdout });
  const ask = () => rl.question("You: ", (line) => {
    const theirMessage = line.trim();
    if (theirMessage === "") return ask();
    if (theirMessage.toLowerCase() === "exit") { console.log("👋 Chat ended. Memory saved."); rl.close(); return; }
    if (theirMessage.toLowerCase() === "reset") {
      resetMemory(); history = [];
      console.log("🧹 Memory cleared. Fresh conversation started.\n");
      return ask();
    }
    const reply = getReply(theirMessage, history);
    history.push({ role: "Them", text: theirMessage });
    history.push({ role: "Me", text: reply });
    saveHistory(history);
    console.log("Twin:", reply, "\n");
    ask();
  });
  ask();
}

function oneShot(theirMessage) {
  if (theirMessage.toLowerCase() === "reset") {
    resetMemory();
    console.log("🧹 Memory cleared. Fresh conversation started.");
    return;
  }
  let history = loadHistory();
  const reply = getReply(theirMessage, history);
  history.push({ role: "Them", text: theirMessage });
  history.push({ role: "Me", text: reply });
  saveHistory(history);
  console.log("\nThem:", theirMessage);
  console.log("You (twin):", reply, "\n");
}

module.exports = { getReply, loadHistory, saveHistory, loadNotes, saveNotes, loadChat, saveChat, resetMemory };

if (require.main === module) {
  const arg = process.argv[2];
  if (!arg) chatLoop();            // no message -> live chat
  else oneShot(arg);              // message -> single reply
}
```

**Run it:** `node core.js` (live chat) · `node core.js "hi"` (one reply) · `node core.js reset` (clear memory)
:::

---

## `server.py` / `server.js` — the web chat UI server (no installs)

:::py
```python
import os
import json
from http.server import HTTPServer, BaseHTTPRequestHandler

import core

HERE = os.path.dirname(os.path.abspath(__file__))
PORT = 8000


class TwinHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        with open(os.path.join(HERE, "index.html"), "r", encoding="utf-8") as f:
            page = f.read()
        self.send_response(200)
        self.send_header("Content-Type", "text/html; charset=utf-8")
        self.end_headers()
        self.wfile.write(page.encode("utf-8"))

    def do_POST(self):
        length = int(self.headers.get("Content-Length", 0))
        data = json.loads(self.rfile.read(length).decode("utf-8"))
        their_message = data.get("message", "").strip()

        history = core.load_history()
        reply = core.get_reply(their_message, history)
        history.append({"role": "Them", "text": their_message})
        history.append({"role": "Me", "text": reply})
        core.save_history(history)

        body = json.dumps({"reply": reply}).encode("utf-8")
        self.send_response(200)
        self.send_header("Content-Type", "application/json; charset=utf-8")
        self.end_headers()
        self.wfile.write(body)

    def log_message(self, *args):
        pass


if __name__ == "__main__":
    print(f"🧑 Twin is live at  http://localhost:{PORT}")
    print("   Open that link in your browser. Press Ctrl+C here to stop.\n")
    HTTPServer(("localhost", PORT), TwinHandler).serve_forever()
```

**Run it:** `python server.py` → open `http://localhost:8000`
:::

:::js
```javascript
const http = require("http");
const fs = require("fs");
const path = require("path");
const core = require("./core");

const HERE = __dirname;
const PORT = 8000;

const server = http.createServer((req, res) => {
  if (req.method === "GET") {
    const page = fs.readFileSync(path.join(HERE, "index.html"), "utf8");
    res.writeHead(200, { "Content-Type": "text/html; charset=utf-8" });
    res.end(page);
  } else if (req.method === "POST") {
    let body = "";
    req.on("data", chunk => { body += chunk; });
    req.on("end", () => {
      let theirMessage = "";
      try { theirMessage = (JSON.parse(body).message || "").trim(); } catch (e) {}
      const history = core.loadHistory();
      const reply = core.getReply(theirMessage, history);
      history.push({ role: "Them", text: theirMessage });
      history.push({ role: "Me", text: reply });
      core.saveHistory(history);
      res.writeHead(200, { "Content-Type": "application/json; charset=utf-8" });
      res.end(JSON.stringify({ reply }));
    });
  }
});

server.listen(PORT, "localhost", () => {
  console.log(`🧑 Twin is live at  http://localhost:${PORT}`);
  console.log("   Open that link in your browser. Press Ctrl+C here to stop.\n");
});
```

**Run it:** `node server.js` → open `http://localhost:8000`
:::

---

## `index.html` — the browser chat page (same for both)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My Twin</title>
  <style>
    * { box-sizing: border-box; font-family: system-ui, "Segoe UI", sans-serif; }
    body { margin: 0; background: #0e1117; color: #e6e6e6; height: 100vh; display: flex; justify-content: center; }
    .app { width: 100%; max-width: 600px; display: flex; flex-direction: column; height: 100vh; }
    header { padding: 16px 20px; background: #161b22; border-bottom: 1px solid #21262d; font-weight: 600; }
    header small { display:block; color:#7d8590; font-weight:400; font-size:12px; margin-top:2px; }
    #chat { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 12px; }
    .bubble { max-width: 78%; padding: 10px 14px; border-radius: 16px; line-height: 1.4; white-space: pre-wrap; }
    .them { align-self: flex-end; background: #2563eb; color: #fff; border-bottom-right-radius: 4px; }
    .twin { align-self: flex-start; background: #21262d; color: #e6e6e6; border-bottom-left-radius: 4px; }
    .inputbar { display: flex; gap: 8px; padding: 14px; background: #161b22; border-top: 1px solid #21262d; }
    #msg { flex: 1; padding: 12px; border-radius: 10px; border: 1px solid #30363d; background: #0e1117; color: #e6e6e6; font-size: 15px; }
    #send { padding: 12px 18px; border: none; border-radius: 10px; background: #2563eb; color: #fff; font-size: 15px; cursor: pointer; }
    #send:disabled { opacity: 0.5; cursor: default; }
  </style>
</head>
<body>
  <div class="app">
    <header>My Twin 🧑<small>Talks like you. Type a message below.</small></header>
    <div id="chat"></div>
    <div class="inputbar">
      <input id="msg" placeholder="Type a message..." autocomplete="off">
      <button id="send">Send</button>
    </div>
  </div>
  <script>
    const chat = document.getElementById("chat");
    const input = document.getElementById("msg");
    const sendBtn = document.getElementById("send");
    function addBubble(text, who) {
      const div = document.createElement("div");
      div.className = "bubble " + who;
      div.textContent = text;
      chat.appendChild(div);
      chat.scrollTop = chat.scrollHeight;
      return div;
    }
    async function send() {
      const text = input.value.trim();
      if (!text) return;
      addBubble(text, "them");
      input.value = "";
      sendBtn.disabled = true;
      const typing = addBubble("typing...", "twin");
      try {
        const res = await fetch("/chat", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ message: text })
        });
        const data = await res.json();
        typing.remove();
        addBubble(data.reply, "twin");
      } catch (e) {
        typing.remove();
        addBubble("[error talking to the server]", "twin");
      }
      sendBtn.disabled = false;
      input.focus();
    }
    sendBtn.addEventListener("click", send);
    input.addEventListener("keydown", e => { if (e.key === "Enter") send(); });
    input.focus();
  </script>
</body>
</html>
```

> ⚠️ Note: the server POSTs to `/chat`. Keep the HTML `fetch("/chat", ...)` and the server's
> POST handler in agreement.

---

## `telegram_bot.py` / `telegram_bot.js` — Telegram bot (no extra installs)

:::py
```python
import os
import json
import urllib.request
import urllib.error

import core

HERE = os.path.dirname(os.path.abspath(__file__))

with open(os.path.join(HERE, "bot_token.txt"), "r", encoding="utf-8") as f:
    TOKEN = f.read().strip()

if not TOKEN or TOKEN == "PASTE_YOUR_BOT_TOKEN_HERE":
    print("❌ No bot token. Open bot_token.txt and paste the token from @BotFather.")
    raise SystemExit

API = f"https://api.telegram.org/bot{TOKEN}/"


def api(method, params, timeout=40):
    data = json.dumps(params).encode("utf-8")
    req = urllib.request.Request(API + method, data=data,
                                 headers={"Content-Type": "application/json"})
    with urllib.request.urlopen(req, timeout=timeout) as resp:
        return json.loads(resp.read().decode("utf-8"))


def handle(message):
    chat_id = message["chat"]["id"]
    text = message.get("text")
    if not text:
        api("sendMessage", {"chat_id": chat_id, "text": "abhi sirf text samajhta hu 😅"})
        return
    if text.strip() == "/start":
        api("sendMessage", {"chat_id": chat_id, "text": "heyy 😄 main inka twin hu, bol kya scene hai?"})
        return
    api("sendChatAction", {"chat_id": chat_id, "action": "typing"})
    hist = core.load_chat(chat_id)
    reply = core.get_reply(text, hist)
    hist.append({"role": "Them", "text": text})
    hist.append({"role": "Me", "text": reply})
    core.save_chat(chat_id, hist)
    api("sendMessage", {"chat_id": chat_id, "text": reply})


def main():
    print("🤖 Twin is live on Telegram. Message your bot. Ctrl+C to stop.\n")
    offset = None
    while True:
        try:
            params = {"timeout": 30}
            if offset is not None:
                params["offset"] = offset
            updates = api("getUpdates", params)
            for upd in updates.get("result", []):
                offset = upd["update_id"] + 1
                if "message" in upd:
                    handle(upd["message"])
        except KeyboardInterrupt:
            print("\n👋 Bot stopped.")
            break
        except (urllib.error.URLError, TimeoutError):
            continue
        except Exception as e:
            print("⚠️  Error:", e)
            continue


if __name__ == "__main__":
    main()
```

**Run it:** `python telegram_bot.py` (after pasting your token in `bot_token.txt`)
:::

:::js
```javascript
const fs = require("fs");
const path = require("path");
const core = require("./core");

const HERE = __dirname;
const TOKEN = fs.readFileSync(path.join(HERE, "bot_token.txt"), "utf8").trim();

if (!TOKEN || TOKEN === "PASTE_YOUR_BOT_TOKEN_HERE") {
  console.log("❌ No bot token. Open bot_token.txt and paste the token from @BotFather.");
  process.exit();
}

const API = `https://api.telegram.org/bot${TOKEN}/`;

// Node 20+ has a built-in global fetch — no dependency needed
async function api(method, params) {
  const res = await fetch(API + method, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(params),
  });
  return res.json();
}

async function handle(message) {
  const chatId = message.chat.id;
  const text = message.text;
  if (!text) {
    await api("sendMessage", { chat_id: chatId, text: "abhi sirf text samajhta hu 😅" });
    return;
  }
  if (text.trim() === "/start") {
    await api("sendMessage", { chat_id: chatId, text: "heyy 😄 main inka twin hu, bol kya scene hai?" });
    return;
  }
  await api("sendChatAction", { chat_id: chatId, action: "typing" });
  const hist = core.loadChat(chatId);
  const reply = core.getReply(text, hist);
  hist.push({ role: "Them", text });
  hist.push({ role: "Me", text: reply });
  core.saveChat(chatId, hist);
  await api("sendMessage", { chat_id: chatId, text: reply });
}

async function main() {
  console.log("🤖 Twin is live on Telegram. Message your bot. Ctrl+C to stop.\n");
  let offset;
  while (true) {
    try {
      const params = { timeout: 30 };
      if (offset !== undefined) params.offset = offset;
      const updates = await api("getUpdates", params);
      for (const upd of updates.result || []) {
        offset = upd.update_id + 1;
        if (upd.message) await handle(upd.message);
      }
    } catch (e) {
      await new Promise(r => setTimeout(r, 1000));   // network hiccup — wait and retry
    }
  }
}

main();
```

**Run it:** `node telegram_bot.js` (after pasting your token in `bot_token.txt`)
:::

---

## `slack_bot.py` / `slack_bot.js` — Slack bot (Socket Mode)

:::py
```python
import os
import re
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler

import core

HERE = os.path.dirname(os.path.abspath(__file__))


def load_tokens():
    tokens = {}
    with open(os.path.join(HERE, "slack_tokens.txt"), "r", encoding="utf-8") as f:
        for line in f:
            line = line.strip()
            if "=" in line:
                key, value = line.split("=", 1)
                tokens[key.strip()] = value.strip()
    return tokens


tokens = load_tokens()
BOT_TOKEN = tokens.get("SLACK_BOT_TOKEN", "")
APP_TOKEN = tokens.get("SLACK_APP_TOKEN", "")

if "PASTE" in BOT_TOKEN or "PASTE" in APP_TOKEN or not BOT_TOKEN or not APP_TOKEN:
    print("❌ Tokens missing. Paste your xoxb- and xapp- tokens in slack_tokens.txt.")
    raise SystemExit

app = App(token=BOT_TOKEN)


def reply_in_voice(text, channel):
    hist = core.load_chat(channel)
    reply = core.get_reply(text, hist)
    hist.append({"role": "Them", "text": text})
    hist.append({"role": "Me", "text": reply})
    core.save_chat(channel, hist)
    return reply


@app.event("message")
def on_dm(event, say):
    if event.get("bot_id") or event.get("subtype"):
        return
    text = event.get("text", "").strip()
    if not text:
        return
    say(reply_in_voice(text, event["channel"]))


@app.event("app_mention")
def on_mention(event, say):
    text = re.sub(r"<@[^>]+>", "", event.get("text", "")).strip()
    if not text:
        return
    say(reply_in_voice(text, event["channel"]))


if __name__ == "__main__":
    print("🤖 Twin is live on Slack. DM the bot or @mention it. Ctrl+C to stop.\n")
    SocketModeHandler(app, APP_TOKEN).start()
```

**Run it:** `python -m pip install slack-bolt` then `python slack_bot.py`
:::

:::js
```javascript
const fs = require("fs");
const path = require("path");
const { App } = require("@slack/bolt");
const core = require("./core");

const HERE = __dirname;

function loadTokens() {
  const tokens = {};
  for (const line of fs.readFileSync(path.join(HERE, "slack_tokens.txt"), "utf8").split("\n")) {
    const t = line.trim();
    if (t.includes("=")) {
      const idx = t.indexOf("=");
      tokens[t.slice(0, idx).trim()] = t.slice(idx + 1).trim();
    }
  }
  return tokens;
}

const tokens = loadTokens();
const BOT_TOKEN = tokens.SLACK_BOT_TOKEN || "";
const APP_TOKEN = tokens.SLACK_APP_TOKEN || "";

if (BOT_TOKEN.includes("PASTE") || APP_TOKEN.includes("PASTE") || !BOT_TOKEN || !APP_TOKEN) {
  console.log("❌ Tokens missing. Paste your xoxb- and xapp- tokens in slack_tokens.txt.");
  process.exit();
}

const app = new App({ token: BOT_TOKEN, appToken: APP_TOKEN, socketMode: true });

function replyInVoice(text, channel) {
  const hist = core.loadChat(channel);
  const reply = core.getReply(text, hist);
  hist.push({ role: "Them", text });
  hist.push({ role: "Me", text: reply });
  core.saveChat(channel, hist);
  return reply;
}

app.event("message", async ({ event, say }) => {
  if (event.bot_id || event.subtype) return;
  const text = (event.text || "").trim();
  if (!text) return;
  await say(replyInVoice(text, event.channel));
});

app.event("app_mention", async ({ event, say }) => {
  const text = (event.text || "").replace(/<@[^>]+>/g, "").trim();
  if (!text) return;
  await say(replyInVoice(text, event.channel));
});

(async () => {
  await app.start();
  console.log("🤖 Twin is live on Slack. DM the bot or @mention it. Ctrl+C to stop.\n");
})();
```

**Run it:** `npm install @slack/bolt` then `node slack_bot.js`
:::

---

## `discord_bot.py` / `discord_bot.js` — Discord bot

:::py
```python
import os
import asyncio
import discord

import core

HERE = os.path.dirname(os.path.abspath(__file__))

with open(os.path.join(HERE, "discord_token.txt"), "r", encoding="utf-8") as f:
    TOKEN = f.read().strip()

if not TOKEN or TOKEN == "PASTE_YOUR_DISCORD_BOT_TOKEN_HERE":
    print("❌ No token. Paste your Discord bot token in discord_token.txt.")
    raise SystemExit

intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)


@client.event
async def on_ready():
    print(f"🤖 Twin is live on Discord as {client.user}. DM it or @mention it.")


@client.event
async def on_message(message):
    if message.author == client.user:
        return
    is_dm = isinstance(message.channel, discord.DMChannel)
    mentioned = client.user in message.mentions
    if not (is_dm or mentioned):
        return
    text = message.content.replace(f"<@{client.user.id}>", "").strip()
    if not text:
        return
    chat_id = f"discord:{message.channel.id}"
    hist = core.load_chat(chat_id)
    async with message.channel.typing():
        reply = await asyncio.to_thread(core.get_reply, text, hist)
    hist.append({"role": "Them", "text": text})
    hist.append({"role": "Me", "text": reply})
    core.save_chat(chat_id, hist)
    await message.channel.send(reply)


if __name__ == "__main__":
    client.run(TOKEN)
```

**Run it:** `python -m pip install discord.py` then `python discord_bot.py`
:::

:::js
```javascript
const fs = require("fs");
const path = require("path");
const { Client, GatewayIntentBits, ChannelType, Partials } = require("discord.js");
const core = require("./core");

const HERE = __dirname;
const TOKEN = fs.readFileSync(path.join(HERE, "discord_token.txt"), "utf8").trim();

if (!TOKEN || TOKEN === "PASTE_YOUR_DISCORD_BOT_TOKEN_HERE") {
  console.log("❌ No token. Paste your Discord bot token in discord_token.txt.");
  process.exit();
}

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent,
    GatewayIntentBits.DirectMessages,
  ],
  partials: [Partials.Channel],   // needed to receive DMs
});

client.once("ready", () => {
  console.log(`🤖 Twin is live on Discord as ${client.user.tag}. DM it or @mention it.`);
});

client.on("messageCreate", async (message) => {
  if (message.author.bot) return;
  const isDM = message.channel.type === ChannelType.DM;
  const mentioned = message.mentions.has(client.user);
  if (!isDM && !mentioned) return;
  const text = message.content.replace(`<@${client.user.id}>`, "").trim();
  if (!text) return;
  const chatId = `discord:${message.channel.id}`;
  const hist = core.loadChat(chatId);
  await message.channel.sendTyping();
  const reply = core.getReply(text, hist);
  hist.push({ role: "Them", text });
  hist.push({ role: "Me", text: reply });
  core.saveChat(chatId, hist);
  await message.channel.send(reply);
});

client.login(TOKEN);
```

**Run it:** `npm install discord.js` then `node discord_bot.js`
:::

---

## Dependencies

:::py
`requirements.txt`:
```text
# core.py, server.py and telegram_bot.py need NO installs (standard library only).
# Only install these for the bot you choose:
slack-bolt        # for slack_bot.py
discord.py        # for discord_bot.py
```
Install with: `python -m pip install -r requirements.txt`
:::

:::js
`package.json`:
```json
{
  "name": "my-twin",
  "version": "1.0.0",
  "type": "commonjs",
  "private": true,
  "dependencies": {
    "@slack/bolt": "^4.0.0",
    "discord.js": "^14.16.0"
  }
}
```
`core.js`, `server.js`, and `telegram_bot.js` need NO installs (Node built-ins only). Run `npm install` only for the Slack/Discord bot.
:::

---

## `.gitignore` (keep secrets & runtime data out of git)

```text
memory.json
notes.json
chats.json
bot_token.txt
slack_tokens.txt
discord_token.txt
node_modules
```

---

That's the entire project — in **Python or JavaScript**. With these files you have a personal
AI twin that talks in your voice, remembers conversations, saves facts, runs as a web app, and
goes live on Telegram, Slack, or Discord. 🎉

# Phase 2 — Building Your Twin (≈1 hour)

Three small iterations. Each one *works* before you move on — don't skip the tests or checkpoints.

> Use the **🐍 Python / 🟨 JavaScript** switch at the top to see the commands and code for your language. The 📋 prompts work either way.

> 🟢 **Codex users:** wherever code calls `claude -p --model sonnet`, use `codex exec -`. The 📋 prompts already say this.

---

## Iteration 0 — It replies in your voice (≈20 min) ⭐

**Goal:** Run a command with a message → twin prints a reply that sounds like you.

📋 **PROMPT:**
```
Build Iteration 0. Create my core file (core.py for Python, or core.js for JavaScript) that:
1. Takes a message as a command-line argument.
2. Reads my PERSONALITY.md file.
3. Builds a prompt: "You are my digital twin, reply exactly as I would" + my personality
   + the message.
4. Sends that prompt to the Claude CLI by running `claude -p --model sonnet`, piping the
   prompt through standard input (Python: subprocess with shell=True; Node: child_process
   execSync with the input option). (Codex users: use `codex exec -` instead.)
5. Prints the reply in the terminal.
Handle a missing argument with a clear usage message. Explain each line with a short comment.
```

:::py
💻 **RUN**
```bash
python core.py "bro I got the job!!"
```

🛟 **Fallback code — paste into `core.py` if your AI is slow/out of quota:**
```python
import sys                          # to read what you type after "python core.py"
import subprocess                   # to call the AI CLI (your twin's brain)

# Make sure emojis print correctly in the terminal (Windows safe)
sys.stdout.reconfigure(encoding="utf-8")

# 1) Get the incoming message
if len(sys.argv) < 2:
    print('Usage: python core.py "the message someone sent you"')
    sys.exit()
their_message = sys.argv[1]

# 2) Read your personality (the "soul")
with open("PERSONALITY.md", "r", encoding="utf-8") as f:
    personality = f.read()

# 3) Build one prompt: who you are + the message
prompt = (
    "You are my digital twin. Reply to the message below EXACTLY as I would, "
    "matching my voice, humor, and rules in my personality profile. "
    "Output ONLY the reply text — no quotes, no explanation.\n\n"
    "===== MY PERSONALITY =====\n" + personality +
    "\n===== MESSAGE TO REPLY TO =====\n" + their_message
)

# 4) Send it to the AI CLI and read the reply.
#    Codex users: change "claude -p --model sonnet" to "codex exec -"
result = subprocess.run(
    "claude -p --model sonnet",
    input=prompt, shell=True, capture_output=True, text=True, encoding="utf-8",
)
if result.returncode != 0:
    print("Error from the AI CLI:", result.stderr.strip())
    sys.exit()

# 5) Print the reply — the magic moment
print("\nThem:", their_message)
print("You (twin):", result.stdout.strip(), "\n")
```
:::
:::js
💻 **RUN**
```bash
node core.js "bro I got the job!!"
```

🛟 **Fallback code — paste into `core.js` if your AI is slow/out of quota:**
```javascript
// to read your personality file + call the AI CLI (your twin's brain)
const fs = require("fs");
const { execSync } = require("child_process");

// 1) Get the incoming message (everything after: node core.js)
const theirMessage = process.argv[2];
if (!theirMessage) {
  console.log('Usage: node core.js "the message someone sent you"');
  process.exit();
}

// 2) Read your personality (the "soul")
const personality = fs.readFileSync("PERSONALITY.md", "utf8");

// 3) Build one prompt: who you are + the message
const prompt =
  "You are my digital twin. Reply to the message below EXACTLY as I would, " +
  "matching my voice, humor, and rules in my personality profile. " +
  "Output ONLY the reply text — no quotes, no explanation.\n\n" +
  "===== MY PERSONALITY =====\n" + personality +
  "\n===== MESSAGE TO REPLY TO =====\n" + theirMessage;

// 4) Send it to the AI CLI and read the reply.
//    Codex users: change "claude -p --model sonnet" to "codex exec -"
let reply;
try {
  reply = execSync("claude -p --model sonnet", { input: prompt, encoding: "utf8" }).trim();
} catch (e) {
  console.log("Error from the AI CLI:", e.message);
  process.exit();
}

// 5) Print the reply — the magic moment
console.log("\nThem:", theirMessage);
console.log("You (twin):", reply, "\n");
```
:::

✅ **SUCCESS LOOKS LIKE:**
```
Them: bro I got the job!!
You (twin): arre nice yaar 👏 dekh liya na, party kab hai 😏
```

**Common fixes:**
- `'claude' not recognized` → CLI not installed / not on PATH. Re-run install from *Before You Arrive*, reopen terminal.
- Asks to log in → run `claude` (or `codex login`) once and sign in.
- `PERSONALITY.md` not found → wrong folder, or Phase 1 skipped.

:::py
- `'pip' not recognized` (Windows) → use `python -m pip ...`.
- `UnicodeEncodeError` → keep `sys.stdout.reconfigure(encoding="utf-8")` at the top.
:::
:::js
- `'node' not recognized` → Node isn't installed / not on PATH. Re-install Node, reopen terminal.
- Node prints emojis fine by default — no extra setup needed.
:::

📌 **Checkpoint** — 📋 **PROMPT:**
```
Initialize git here if needed and commit everything with the message
"feat: iteration 0 - twin replies in my voice". Confirm the commit is saved.
```

---

## Iteration 1 — It remembers (≈20 min)

**Goal:** Twin recalls earlier messages, so it can hold a real back-and-forth.

📋 **PROMPT:**
```
Build Iteration 1: add memory to my core file. Save the conversation to a file memory.json
(a list of who-said-what). On each run, load that history and include it in the prompt so
the reply makes sense in context, then append the new message + reply and save it.
Also add a "reset" command that clears memory.json and starts fresh.
Keep using `claude -p --model sonnet` (Codex: `codex exec -`). Keep the comments.
```

:::py
💻 **RUN**
```bash
python core.py reset
python core.py "heyy kaise ho"
python core.py "guess what, I got the job!"
```

🛟 **Fallback code for `core.py`:**
```python
import sys, os, json, subprocess
sys.stdout.reconfigure(encoding="utf-8")

HERE = os.path.dirname(os.path.abspath(__file__))
PERSONALITY_FILE = os.path.join(HERE, "PERSONALITY.md")
MEMORY_FILE = os.path.join(HERE, "memory.json")

if len(sys.argv) < 2:
    print('Usage: python core.py "message"   (or: python core.py reset)')
    sys.exit()
their_message = sys.argv[1]

# reset command: wipe memory
if their_message.lower() == "reset":
    if os.path.exists(MEMORY_FILE):
        os.remove(MEMORY_FILE)
    print("🧹 Memory cleared. Fresh conversation started.")
    sys.exit()

# load history (empty if none yet)
if os.path.exists(MEMORY_FILE):
    with open(MEMORY_FILE, "r", encoding="utf-8") as f:
        history = json.load(f)
else:
    history = []

with open(PERSONALITY_FILE, "r", encoding="utf-8") as f:
    personality = f.read()

# turn history into text
history_text = ""
for turn in history:
    history_text += turn["role"] + ": " + turn["text"] + "\n"
if history_text == "":
    history_text = "(no messages yet — start of the conversation)"

prompt = (
    "You are my digital twin. Reply to the latest message EXACTLY as I would, using the "
    "conversation so far for context. Output ONLY the reply text.\n\n"
    "===== MY PERSONALITY =====\n" + personality +
    "\n===== CONVERSATION SO FAR =====\n" + history_text +
    "\n===== LATEST MESSAGE =====\n" + their_message
)

# Codex users: change "claude -p --model sonnet" to "codex exec -"
result = subprocess.run(
    "claude -p --model sonnet",
    input=prompt, shell=True, capture_output=True, text=True, encoding="utf-8",
)
if result.returncode != 0:
    print("Error from the AI CLI:", result.stderr.strip())
    sys.exit()
reply = result.stdout.strip()

# save the new turn
history.append({"role": "Them", "text": their_message})
history.append({"role": "Me", "text": reply})
with open(MEMORY_FILE, "w", encoding="utf-8") as f:
    json.dump(history, f, ensure_ascii=False, indent=2)

print("\nThem:", their_message)
print("You (twin):", reply, "\n")
```
:::
:::js
💻 **RUN**
```bash
node core.js reset
node core.js "heyy kaise ho"
node core.js "guess what, I got the job!"
```

🛟 **Fallback code for `core.js`:**
```javascript
const fs = require("fs");
const path = require("path");
const { execSync } = require("child_process");

const HERE = __dirname;
const PERSONALITY_FILE = path.join(HERE, "PERSONALITY.md");
const MEMORY_FILE = path.join(HERE, "memory.json");

const theirMessage = process.argv[2];
if (!theirMessage) {
  console.log('Usage: node core.js "message"   (or: node core.js reset)');
  process.exit();
}

// reset command: wipe memory
if (theirMessage.toLowerCase() === "reset") {
  if (fs.existsSync(MEMORY_FILE)) fs.rmSync(MEMORY_FILE);
  console.log("🧹 Memory cleared. Fresh conversation started.");
  process.exit();
}

// load history (empty if none yet)
let history = [];
if (fs.existsSync(MEMORY_FILE)) {
  history = JSON.parse(fs.readFileSync(MEMORY_FILE, "utf8"));
}

const personality = fs.readFileSync(PERSONALITY_FILE, "utf8");

// turn history into text
let historyText = "";
for (const turn of history) historyText += turn.role + ": " + turn.text + "\n";
if (historyText === "") historyText = "(no messages yet — start of the conversation)";

const prompt =
  "You are my digital twin. Reply to the latest message EXACTLY as I would, using the " +
  "conversation so far for context. Output ONLY the reply text.\n\n" +
  "===== MY PERSONALITY =====\n" + personality +
  "\n===== CONVERSATION SO FAR =====\n" + historyText +
  "\n===== LATEST MESSAGE =====\n" + theirMessage;

// Codex users: change "claude -p --model sonnet" to "codex exec -"
let reply;
try {
  reply = execSync("claude -p --model sonnet", { input: prompt, encoding: "utf8" }).trim();
} catch (e) {
  console.log("Error from the AI CLI:", e.message);
  process.exit();
}

// save the new turn
history.push({ role: "Them", text: theirMessage });
history.push({ role: "Me", text: reply });
fs.writeFileSync(MEMORY_FILE, JSON.stringify(history, null, 2));

console.log("\nThem:", theirMessage);
console.log("You (twin):", reply, "\n");
```
:::

✅ **SUCCESS LOOKS LIKE:** the second reply reacts in context (e.g. "arre that's great!") without you re-explaining anything.

**Common fixes:**
- Twin "forgets" → check `memory.json` is being created in this folder (you may be in another folder).
- Garbled emojis in `memory.json` → :::py keep `ensure_ascii=False` when saving. ::: :::js Node's `JSON.stringify` keeps emojis fine. :::

📌 **Checkpoint** — 📋 **PROMPT:**
```
Commit with message "feat: iteration 1 - conversation memory". Confirm it's saved.
```

---

## Iteration 2 — Live chat loop (≈20 min)

**Goal:** Type turn-by-turn in one window, no re-running the command.

📋 **PROMPT:**
```
Build Iteration 2: add an interactive chat loop to my core file. If I run it with NO message
(`python core.py` / `node core.js`), open a loop where I type, the twin replies, and it keeps
going — saving to memory.json each turn. Inside the loop: typing 'exit' quits, 'reset' clears
memory. KEEP the old behavior: running with a message still does a one-shot reply, and "reset"
still clears memory. Refactor the reply logic into a function both modes share.
Keep using `claude -p --model sonnet` (Codex: `codex exec -`).
```

:::py
💻 **RUN** (then just type; `exit` to quit)
```bash
python core.py
```

🛟 **Fallback code for `core.py`:**
```python
import sys, os, json, subprocess
sys.stdout.reconfigure(encoding="utf-8")

HERE = os.path.dirname(os.path.abspath(__file__))
PERSONALITY_FILE = os.path.join(HERE, "PERSONALITY.md")
MEMORY_FILE = os.path.join(HERE, "memory.json")

def load_history():
    if os.path.exists(MEMORY_FILE):
        with open(MEMORY_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    return []

def save_history(history):
    with open(MEMORY_FILE, "w", encoding="utf-8") as f:
        json.dump(history, f, ensure_ascii=False, indent=2)

def get_reply(their_message, history):
    with open(PERSONALITY_FILE, "r", encoding="utf-8") as f:
        personality = f.read()
    history_text = ""
    for turn in history:
        history_text += turn["role"] + ": " + turn["text"] + "\n"
    if history_text == "":
        history_text = "(start of the conversation)"
    prompt = (
        "You are my digital twin. Reply to the latest message EXACTLY as I would, using the "
        "conversation so far for context. Output ONLY the reply text.\n\n"
        "===== MY PERSONALITY =====\n" + personality +
        "\n===== CONVERSATION SO FAR =====\n" + history_text +
        "\n===== LATEST MESSAGE =====\n" + their_message
    )
    # Codex users: change "claude -p --model sonnet" to "codex exec -"
    result = subprocess.run(
        "claude -p --model sonnet",
        input=prompt, shell=True, capture_output=True, text=True, encoding="utf-8",
    )
    if result.returncode != 0:
        return "[error from the AI CLI: " + result.stderr.strip() + "]"
    return result.stdout.strip()

def reset_memory():
    if os.path.exists(MEMORY_FILE):
        os.remove(MEMORY_FILE)

def chat_loop():
    print("\n🧑 Your twin is online. Type a message (or 'exit' to quit, 'reset' to clear).\n")
    history = load_history()
    while True:
        try:
            msg = input("You: ").strip()
        except (KeyboardInterrupt, EOFError):
            print("\n👋 Chat ended. Memory saved."); break
        if msg == "":
            continue
        if msg.lower() == "exit":
            print("👋 Chat ended. Memory saved."); break
        if msg.lower() == "reset":
            reset_memory(); history = []; print("🧹 Memory cleared.\n"); continue
        reply = get_reply(msg, history)
        history.append({"role": "Them", "text": msg})
        history.append({"role": "Me", "text": reply})
        save_history(history)
        print("Twin:", reply, "\n")

def one_shot(their_message):
    if their_message.lower() == "reset":
        reset_memory(); print("🧹 Memory cleared."); return
    history = load_history()
    reply = get_reply(their_message, history)
    history.append({"role": "Them", "text": their_message})
    history.append({"role": "Me", "text": reply})
    save_history(history)
    print("\nThem:", their_message)
    print("You (twin):", reply, "\n")

if __name__ == "__main__":
    if len(sys.argv) < 2:
        chat_loop()
    else:
        one_shot(sys.argv[1])
```
:::
:::js
💻 **RUN** (then just type; `exit` to quit)
```bash
node core.js
```

🛟 **Fallback code for `core.js`:**
```javascript
const fs = require("fs");
const path = require("path");
const { execSync } = require("child_process");
const readline = require("readline");

const HERE = __dirname;
const PERSONALITY_FILE = path.join(HERE, "PERSONALITY.md");
const MEMORY_FILE = path.join(HERE, "memory.json");

function loadHistory() {
  if (fs.existsSync(MEMORY_FILE)) return JSON.parse(fs.readFileSync(MEMORY_FILE, "utf8"));
  return [];
}
function saveHistory(history) {
  fs.writeFileSync(MEMORY_FILE, JSON.stringify(history, null, 2));
}
function getReply(theirMessage, history) {
  const personality = fs.readFileSync(PERSONALITY_FILE, "utf8");
  let historyText = "";
  for (const turn of history) historyText += turn.role + ": " + turn.text + "\n";
  if (historyText === "") historyText = "(start of the conversation)";
  const prompt =
    "You are my digital twin. Reply to the latest message EXACTLY as I would, using the " +
    "conversation so far for context. Output ONLY the reply text.\n\n" +
    "===== MY PERSONALITY =====\n" + personality +
    "\n===== CONVERSATION SO FAR =====\n" + historyText +
    "\n===== LATEST MESSAGE =====\n" + theirMessage;
  // Codex users: change "claude -p --model sonnet" to "codex exec -"
  try {
    return execSync("claude -p --model sonnet", { input: prompt, encoding: "utf8" }).trim();
  } catch (e) {
    return "[error from the AI CLI: " + e.message + "]";
  }
}
function resetMemory() {
  if (fs.existsSync(MEMORY_FILE)) fs.rmSync(MEMORY_FILE);
}

function oneShot(theirMessage) {
  if (theirMessage.toLowerCase() === "reset") { resetMemory(); console.log("🧹 Memory cleared."); return; }
  const history = loadHistory();
  const reply = getReply(theirMessage, history);
  history.push({ role: "Them", text: theirMessage });
  history.push({ role: "Me", text: reply });
  saveHistory(history);
  console.log("\nThem:", theirMessage);
  console.log("You (twin):", reply, "\n");
}

function chatLoop() {
  console.log("\n🧑 Your twin is online. Type a message (or 'exit' to quit, 'reset' to clear).\n");
  let history = loadHistory();
  const rl = readline.createInterface({ input: process.stdin, output: process.stdout });
  const ask = () => {
    rl.question("You: ", (raw) => {
      const msg = raw.trim();
      if (msg === "") return ask();
      if (msg.toLowerCase() === "exit") { console.log("👋 Chat ended. Memory saved."); rl.close(); return; }
      if (msg.toLowerCase() === "reset") { resetMemory(); history = []; console.log("🧹 Memory cleared.\n"); return ask(); }
      const reply = getReply(msg, history);
      history.push({ role: "Them", text: msg });
      history.push({ role: "Me", text: reply });
      saveHistory(history);
      console.log("Twin:", reply, "\n");
      ask();
    });
  };
  ask();
}

// no message → live chat loop; a message → one-shot reply
if (process.argv.length < 3) {
  chatLoop();
} else {
  oneShot(process.argv[2]);
}
```
:::

✅ **SUCCESS LOOKS LIKE:**
```
You: kya kar raha hai
Twin: bas yaar kuch khaas nahi, tu bata kya scene hai
You: exit
👋 Chat ended. Memory saved.
```

**Common fixes:**
- Loop exits instantly → you piped input; just run the command and type.
- `Ctrl+C` → it's handled; ends the chat and saves memory.

📌 **Checkpoint** — 📋 **PROMPT:**
```
Commit with message "feat: iteration 2 - interactive chat loop". Confirm it's saved.
```

---

### 🎁 Optional bonus — web chat page
Want a browser chat window? 📋 paste: `Add a simple web chat UI: a tiny server (Python's built-in http.server, or Node's built-in http module) that serves an index.html chat page and calls my reply function. No extra installs.` (Full code in the **Reference Code** appendix, both languages.)

---

Your twin talks like you and remembers. → **Next: Phase 3, Go Live.**

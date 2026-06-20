# Phase 4 — Capabilities: teach your twin to *do* things (~35 min)

Your twin **talks** like you and lives on your chat app — that's the foundation. Now make it **do** things. There are **30 capabilities** below — reminders, real answers, **Voice**, long-term memory, and more. Pick any; add them like LEGO bricks. (Voice is just *one* of the 30 — optional, not the goal.)

> 🎯 **Goal:** add **at least ONE** capability and get it working. Got time/quota? Club a few. Each one works on **all** your interfaces at once (terminal, web, chat platform) — they share one brain (the `get_reply` / `getReply` function).

---

## The "marker pattern" (read once)

Every capability uses the same trick:

```
You/them: "remind me to call mom at 6"
   │
   ▼
twin replies normally  +  hides a secret marker:  [REMIND: call mom at 6pm]
   │
   ▼
your code (Python or JS) spots [REMIND: ...] with a regex
   │
   ▼
it DOES the thing (saves the reminder) and DELETES the marker
   │
   ▼
you only see the natural reply: "haan yaad dila dungi 🙏"
```

The AI decides *when* to act by writing a marker; your code does the action and hides it. Learn it once below — all 30 follow this shape.

---

## ⭐ Worked example: Reminders / to-do

Build this one fully, then every other capability is the same pattern with small tweaks.

**Goal:** ask the twin to remember a task → it saves it. Say "show reminders" → it lists them in your voice.

📋 **PROMPT — paste into your AI tool**
```
Add a "reminders" capability to my twin, following the same marker pattern as the
existing notes feature in core.py / core.js.

1. In get_reply, add an instruction telling the model: if the latest message asks
   me to remember a task or set a reminder, end the reply with a hidden line in EXACTLY
   this format: [REMIND: the task in a short sentence].
2. Also load any saved reminders from a file reminders.json and include them in the
   prompt under a "MY REMINDERS" section, so the twin can read them back when asked
   things like "show reminders" / "mere reminders kya hai".
3. After getting the reply, use a regex to find [REMIND: ...] lines, append each task to
   reminders.json, and strip those lines from the shown reply (exactly like notes).
Keep it simple, add short comments, and don't break the existing notes feature.
```

> 🟢 **Codex users:** same prompt — just paste it into Codex.

:::py
💻 **RUN — test it**
```bash
python core.py reset
python core.py "yaar remind me to submit the report kal subah"
python core.py "aur ek - call the plumber at 5pm"
python core.py "show me my reminders"
```
:::
:::js
💻 **RUN — test it**
```bash
node core.js reset
node core.js "yaar remind me to submit the report kal subah"
node core.js "aur ek - call the plumber at 5pm"
node core.js "show me my reminders"
```
:::

✅ **SUCCESS LOOKS LIKE**
```
Them: show me my reminders
You (twin): haan ye rahe — 1) submit the report kal subah  2) call the plumber 5pm 🙏
```
A `reminders.json` now exists with both tasks. (Markers must NEVER show in the reply — if they do, tell your AI: "the marker is leaking, strip it before printing.")

:::py
🛟 **Out of quota? Add this to `core.py`** — near the other file paths:
```python
REMINDERS_FILE = os.path.join(HERE, "reminders.json")

def load_reminders():
    if os.path.exists(REMINDERS_FILE):
        with open(REMINDERS_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    return []

def save_reminders(reminders):
    with open(REMINDERS_FILE, "w", encoding="utf-8") as f:
        json.dump(reminders, f, ensure_ascii=False, indent=2)
```
Inside `get_reply()`, add a reminders section to the prompt and, after the reply, extract + save + strip the markers (mirror the notes code):
```python
    reminders = load_reminders()
    rem_text = "\n".join("- " + r for r in reminders) or "(none yet)"
    # ...add a "===== MY REMINDERS =====\n" + rem_text section to the prompt...

    # after reply = result.stdout.strip():
    found = re.findall(r"\[REMIND:\s*(.+?)\]", reply)
    if found:
        for task in found:
            task = task.strip()
            if task and task not in reminders:
                reminders.append(task)
        save_reminders(reminders)
        reply = re.sub(r"\s*\[REMIND:\s*.+?\]", "", reply).strip()
```
Also add the `[REMIND: ...]` instruction line to the prompt text.
:::
:::js
🛟 **Out of quota? Add this to `core.js`** — near the other file paths (assumes `const fs = require('fs'); const path = require('path'); const HERE = __dirname;`):
```javascript
const REMINDERS_FILE = path.join(HERE, "reminders.json");

function loadReminders() {
  if (fs.existsSync(REMINDERS_FILE))
    return JSON.parse(fs.readFileSync(REMINDERS_FILE, "utf8"));
  return [];
}

function saveReminders(reminders) {
  fs.writeFileSync(REMINDERS_FILE, JSON.stringify(reminders, null, 2));
}
```
Inside `getReply()`, add a reminders section to the prompt and, after the reply, extract + save + strip the markers (mirror the notes code):
```javascript
  const reminders = loadReminders();
  const remText = reminders.map(r => "- " + r).join("\n") || "(none yet)";
  // ...add a "===== MY REMINDERS =====\n" + remText section to the prompt...

  // after: let reply = execSync("claude -p --model sonnet", {input: prompt, encoding:"utf8"}).trim();
  const found = [...reply.matchAll(/\[REMIND:\s*(.+?)\]/g)].map(m => m[1].trim());
  if (found.length) {
    for (const task of found)
      if (task && !reminders.includes(task)) reminders.push(task);
    saveReminders(reminders);
    reply = reply.replace(/\s*\[REMIND:\s*.+?\]/g, "").trim();
  }
```
Also add the `[REMIND: ...]` instruction line to the prompt text.
:::

✅ **Checkpoint** — paste: `commit my work with message "feat: reminders capability"`

> 🔋 You just learned the pattern behind **all 30**. Each one below is one prompt away.

---

## The full menu — 30 capabilities

Pick any. Each has a ready 📋 prompt — paste it, test with 2–3 short messages, checkpoint.
**Difficulty:** 🟢 easy (pure marker) · 🟡 needs a library/API key · 🔴 advanced.

> ℹ️ Already built: **Notes/facts**, **persistent memory**, your **chat platform** — you've done a few already!

### 🟢 Tier 1 — Beginner (great for today)

| # | Capability | What it does | 📋 Prompt to paste |
|---|-----------|--------------|--------------------|
| 1 | Notes / facts ✅ DONE | Saves & recalls facts | *(already built)* |
| 2 | Reminders / to-do | Saves tasks, lists them back | *(worked example above)* |
| 3 | Per-person memory | Separate memory per contact | "Make memory separate per person: key the notes/history by the chat or sender id, so different people don't get mixed up." |
| 4 | Contact profiles | Learns each person's name/role | "Add a [PROFILE: name=…, context=…] marker that saves a small profile per contact, and include it in the prompt so the twin remembers who it's talking to." |
| 5 | Forget / edit memory | "bhool ja that" removes a fact | "Add a [FORGET: ...] marker so when I tell the twin to forget something, it removes the matching fact from notes.json." |
| 6 | Quick math | Accurate calculations | "Add a [CALC: expression] marker; when present, compute it safely in code and put the exact answer in the reply." |
| 7 | Time / date / countdown | Real time, 'kitne din baaki' | "Add a [TIME] and [COUNTDOWN: date] marker that gives the real current date/time and days-remaining (Python datetime / JS Date)." |
| 8 | Mood / tone modes | "professional" vs "friends" | "Add a tone mode: a command like 'switch to professional mode' that swaps a tone block in the prompt and is remembered per chat." |
| 9 | Auto-away mode | Auto-replies only when away | "Add an 'away' on/off switch saved to a file; when away is off, the bot doesn't auto-reply." |
| 10 | Daily digest | Who messaged while away | "Add a 'digest' command that summarises the saved conversations (chats.json) into a short who-wanted-what list in my voice." |
| 11 | Multi-language match | Replies in their language | "Add a rule to PERSONALITY.md so the twin replies in whatever language the other person used (Hindi, English, Hinglish, etc.)." |
| 12 | Approval mode | Drafts, I approve before send | "Add an approval mode: instead of auto-sending, print the draft and wait for me to type 'send' before it goes out." |
| 13 | Currency / unit convert | "$50 kitne rupaye" | "Add a [CONVERT: from->to value] marker that converts currencies/units and puts the result in the reply (use a fixed rate table for now)." |

### 🟡 Tier 2 — Intermediate (needs a library or API key)

| # | Capability | What it does | 📋 Prompt to paste |
|---|-----------|--------------|--------------------|
| 14 | Answer real questions | Looks things up | "Add a [SEARCH: query] marker; when present, run a web search (use a free search API or the CLI's web tool) and let the twin answer from the results." |
| 15 | Weather | "kal barish hogi?" | "Add a [WEATHER: city] marker that calls a free weather API and replies with the forecast in my voice. Explain where to put the API key." |
| 16 | News / headlines | Top news / topic digest | "Add a [NEWS: topic] marker that fetches headlines from a news API and summarises them in my voice." |
| 17 | Summarize a URL | Link → summary in your voice | "If a message contains a URL, fetch the page text and have the twin summarise it briefly in my voice." |
| 18 | Calendar (Google) | Create events | "Add a [CAL: ...] capability using the Google Calendar API to create events; walk me through the Google credentials setup." |
| 19 | Send email (Gmail) | Drafts & sends mail | "Add a [EMAIL: to|subject|body] marker that sends email via the Gmail API; guide me through enabling it." |
| 20 | Notion / Trello tasks | Pushes items to my board | "Add a [TASK: ...] marker that creates a card in Notion or Trello via their API." |
| 24 | Group-chat awareness | Knows who said what | "When in a group chat, pass each sender's name into the history so the twin knows who said what." |

#### 🎙️ #21 Voice replies (TTS) — your twin *speaks*
📋 **PROMPT:** *"After generating a reply, also convert it to speech and save/play an audio file. Use a TTS tool — keep text working too."*
:::py
🛟 Offline starter: `pip install pyttsx3` → `engine.say(reply); engine.runAndWait()`.
:::
:::js
🛟 Offline starter: `npm install say` → `require('say').speak(reply)` (or an ElevenLabs/OpenAI TTS `fetch`).
:::

#### 🎙️ #22 Voice notes in (STT) — talk *to* your twin
📋 **PROMPT:** *"When the platform sends a voice note, download the audio, transcribe it with a speech-to-text tool (OpenAI Whisper API, or `faster-whisper` in Python / `nodejs-whisper` in Node), then feed that text into get_reply."*

#### ⏰ #23 Cronjobs / scheduled actions — the twin acts on a schedule
📋 **PROMPT:** *"Add a background scheduler so my twin runs tasks at set times — e.g. every morning at 9am send me my reminders. Keep the schedule in a file I can edit."*
:::py
🛟 `pip install schedule`; run a small loop in a thread that checks the time. (Pairs with Reminders #2.)
:::
:::js
🛟 `npm install node-cron`; `cron.schedule('0 9 * * *', () => { /* send reminders */ })`. (Pairs with Reminders #2.)
:::

### 🔴 Tier 3 — Advanced (great after-event projects)

| # | Capability | What it does | 📋 Prompt to paste |
|---|-----------|--------------|--------------------|
| 26 | Semantic memory (RAG) | Finds past messages by meaning | "Add semantic search over my saved chats using embeddings + a vector store, and feed the top matches into get_reply." |
| 28 | Read my documents (RAG) | Answers from my PDFs/notes | "Let me drop PDFs in a folder; index them and have the twin answer questions from them." |
| 29 | WhatsApp | Replies from my number | "Help me connect the twin to WhatsApp using the WhatsApp Business API or a provider like Twilio." |
| 30 | Multi-step automation | Chains actions for one request | "Let the twin plan and run a few steps in sequence for one request (e.g. check calendar → draft email → ask me to approve)." |

#### 🤖 #25 Subagents / delegation — your twin gets a team
📋 **PROMPT:** *"Add a [DELEGATE: task] capability: when a request is big, my twin calls the CLI brain again as a separate helper for each sub-task, collects the results, and replies with the combined answer. Keep it to 2–3 helpers max."*
🔋 Each helper is another AI call — uses more quota, try it once near the end.

#### 🧠 #27 Engram — a REAL long-term memory ⭐ (GrowthX repo)
[Engram](https://github.com/anmolm-growthx/engram-memory) is GrowthX's plug-and-play memory: stores everything in one SQLite file, recalls the *most relevant* memories with hybrid semantic + lexical search — **no API keys to start**. It's a **Node package**, so on the JavaScript path it's native (and works just as well from Python via its CLI). Needs Node ≥ 20 (you have it).

💻 **Install + try the CLI**
```bash
npm install github:anmolm-growthx/engram-memory
engram add "Yamini is building a personal AI twin at the GrowthX buildathon"
engram add "Yamini doesn't like cricket"
engram recall "what is Yamini working on" -k 3
```
You get ranked, *explainable* memories back.

📋 **PROMPT — wire it into your twin**
```
Integrate the Engram memory CLI (already installed) into core.py / core.js:
- Before building the prompt in get_reply, run `engram recall "<the user's message>" -k 5`
  and include the returned memories in a "LONG-TERM MEMORY" section of the prompt.
- After replying, run `engram add "<a one-line summary of anything important from this turn>"`
  so the twin's memory grows over time.
Call engram via subprocess (Python) / child_process (Node), just like we call the claude CLI.
Keep it simple and add comments.
```
✅ **SUCCESS:** mention something now, restart everything later — the twin still remembers it, pulled from Engram.

---

## 🏁 Wrap-up

- **Add ONE capability fully** — Reminders #2 or Per-person memory #3 are the best first picks. Test, **checkpoint**, then add more if time/quota allow.
- A few that *work* beat many that half-work.
- 🔋 Adding a capability is one cheap prompt; **testing** burns quota — keep tests to 2–3 short messages.

→ **Next: Phase 5 — Tune your twin's style and demo it.**

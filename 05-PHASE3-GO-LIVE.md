# Phase 3 — Go Live on a Real Platform 🎉 (~30 min)

Put your twin on a real chat app so people can message it — and it replies in your voice, even when you're away.

> **Pick ONE platform today.** All three share the same brain (`core.get_reply` / `getReply`) and memory (`chats.json`), so anything you build later works everywhere. Add the others after the event.

## Which one?

| Platform | Effort | Needs | Pick it if… |
|----------|--------|-------|-------------|
| **Telegram** ⭐ | Easiest | Nothing to install | You want the fastest win — token in 60s. |
| **Slack** | Medium | Bolt SDK + 2 tokens | Your team/college uses Slack. |
| **Discord** | Medium | Discord lib + 1 toggle | You're on Discord with friends. |

**New here? Pick Telegram.**

> 🔋 Every message your bot answers uses AI quota. Test with 2–3 messages, not 20.

---

# 🅰️ Telegram (⭐ easiest)

### Step 1 — Create the bot
1. In Telegram, search **@BotFather** (blue tick).
2. Send `/newbot`.
3. Give a **name** ("My Twin") and a **username** ending in `bot` (`harsh_twin_bot`).
4. Copy the **token** it sends (`7123456789:AAH...`).

### Step 2 — Save the token
Put *only* the token in a new file **`bot_token.txt`** in your project folder.

### Step 3 — Generate the bot

:::py
📋 **PROMPT — paste into your AI tool**
```
Create a file called telegram_bot.py. It should:
- read the bot token from bot_token.txt
- import my existing core.py and reuse core.get_reply, core.load_chat, core.save_chat
- use Python's built-in urllib (NO extra libraries) to long-poll Telegram getUpdates and send replies with sendMessage
- keep a separate, persistent conversation per chat using core.load_chat / core.save_chat
- show a "typing" action while it thinks
- reply to /start with a friendly intro
Keep it simple and well-commented for a beginner.
```
:::
:::js
📋 **PROMPT — paste into your AI tool**
```
Create a file called telegram_bot.js. It should:
- read the bot token from bot_token.txt
- require my existing core.js and reuse getReply, loadChat, saveChat
- use Node's built-in global fetch (Node 20+, NO extra libraries) to long-poll Telegram getUpdates and send replies with sendMessage
- keep a separate, persistent conversation per chat using loadChat / saveChat
- show a "typing" action while it thinks
- reply to /start with a friendly intro
Keep it simple and well-commented for a beginner.
```
:::

### Step 4 — Run it

:::py
💻 **RUN**
```bash
python telegram_bot.py
```
:::
:::js
💻 **RUN**
```bash
node telegram_bot.js
```
:::

✅ **SUCCESS LOOKS LIKE**
```
🤖 Twin is live on Telegram. Message your bot from any Telegram app.
```
Open Telegram, find your bot, message it → your twin replies. 🎉

:::py
🛟 **Out of quota? Paste this into `telegram_bot.py`**
```python
import os
import json
import urllib.request          # built-in HTTP client — no pip installs needed
import urllib.error

import core                    # reuse the twin's brain (get_reply) + persistent memory

HERE = os.path.dirname(os.path.abspath(__file__))

with open(os.path.join(HERE, "bot_token.txt"), "r", encoding="utf-8") as f:
    TOKEN = f.read().strip()

if not TOKEN or TOKEN == "PASTE_YOUR_BOT_TOKEN_HERE":
    print("❌ No bot token. Open bot_token.txt and paste the token from @BotFather.")
    raise SystemExit

API = f"https://api.telegram.org/bot{TOKEN}/"


def api(method, params, timeout=40):
    """Call a Telegram API method with a JSON body and return the parsed response."""
    data = json.dumps(params).encode("utf-8")
    req = urllib.request.Request(API + method, data=data,
                                 headers={"Content-Type": "application/json"})
    with urllib.request.urlopen(req, timeout=timeout) as resp:
        return json.loads(resp.read().decode("utf-8"))


def handle(message):
    chat_id = message["chat"]["id"]
    text = message.get("text")

    if not text:
        api("sendMessage", {"chat_id": chat_id, "text": "abhi sirf text samajhti hu yaar 😅"})
        return

    if text.strip() == "/start":
        api("sendMessage", {"chat_id": chat_id,
                            "text": "heyy 😄 main inka twin hu, bol kya scene hai?"})
        return

    api("sendChatAction", {"chat_id": chat_id, "action": "typing"})

    hist = core.load_chat(chat_id)               # persistent per-chat memory
    reply = core.get_reply(text, hist)
    hist.append({"role": "Them", "text": text})
    hist.append({"role": "Me", "text": reply})
    core.save_chat(chat_id, hist)

    api("sendMessage", {"chat_id": chat_id, "text": reply})


def main():
    print("🤖 Twin is live on Telegram. Message your bot from any Telegram app.")
    print("   Press Ctrl+C here to stop.\n")
    offset = None

    while True:
        try:
            params = {"timeout": 30}             # long-poll: wait up to 30s for a message
            if offset is not None:
                params["offset"] = offset
            updates = api("getUpdates", params)

            for upd in updates.get("result", []):
                offset = upd["update_id"] + 1     # don't reprocess this update
                if "message" in upd:
                    handle(upd["message"])

        except KeyboardInterrupt:
            print("\n👋 Bot stopped.")
            break
        except (urllib.error.URLError, TimeoutError):
            continue                              # network hiccup / poll timeout — retry
        except Exception as e:
            print("⚠️  Error:", e)
            continue


if __name__ == "__main__":
    main()
```
:::
:::js
🛟 **Out of quota? Paste this into `telegram_bot.js`**
```javascript
const fs = require("fs");
const path = require("path");
const core = require("./core.js");   // reuse the twin's brain (getReply) + persistent memory

const TOKEN = fs.readFileSync(path.join(__dirname, "bot_token.txt"), "utf8").trim();
if (!TOKEN || TOKEN === "PASTE_YOUR_BOT_TOKEN_HERE") {
  console.log("❌ No bot token. Open bot_token.txt and paste the token from @BotFather.");
  process.exit(1);
}

const API = `https://api.telegram.org/bot${TOKEN}/`;

// Call a Telegram API method with a JSON body (uses Node 20+ built-in fetch — no installs)
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
    await api("sendMessage", { chat_id: chatId, text: "abhi sirf text samajhti hu yaar 😅" });
    return;
  }
  if (text.trim() === "/start") {
    await api("sendMessage", { chat_id: chatId, text: "heyy 😄 main inka twin hu, bol kya scene hai?" });
    return;
  }

  await api("sendChatAction", { chat_id: chatId, action: "typing" });

  const hist = core.loadChat(chatId);            // persistent per-chat memory
  const reply = core.getReply(text, hist);
  hist.push({ role: "Them", text });
  hist.push({ role: "Me", text: reply });
  core.saveChat(chatId, hist);

  await api("sendMessage", { chat_id: chatId, text: reply });
}

async function main() {
  console.log("🤖 Twin is live on Telegram. Message your bot from any Telegram app.");
  console.log("   Press Ctrl+C here to stop.\n");
  let offset;

  while (true) {
    try {
      const params = { timeout: 30 };            // long-poll: wait up to 30s for a message
      if (offset !== undefined) params.offset = offset;
      const updates = await api("getUpdates", params);

      for (const upd of updates.result || []) {
        offset = upd.update_id + 1;              // don't reprocess this update
        if (upd.message) await handle(upd.message);
      }
    } catch (e) {
      // network hiccup / poll timeout — just retry
    }
  }
}

main();
```
:::

### Troubleshooting (Telegram)
- **No reply** → send `/start` in the chat first; Telegram requires it.
- **"No bot token"** → `bot_token.txt` is empty or still a placeholder.
- **Nothing happens** → keep the run window (`python telegram_bot.py` / `node telegram_bot.js`) open.

---

# 🅱️ Slack (if your team uses Slack)

Uses **Socket Mode** — no public URL, runs from your laptop.

### Step 1 — Create the app
1. **https://api.slack.com/apps** → **Create New App** → **From scratch**.
2. Name it, pick your workspace → **Create App**.

### Step 2 — Enable Socket Mode (gives the app token)
3. **Socket Mode** → toggle **ON**.
4. Generate an **App-Level Token** with scope **`connections:write`** → copy the **`xapp-...`** token.

### Step 3 — Add bot scopes
5. **OAuth & Permissions** → **Bot Token Scopes**, add: `chat:write`, `im:history`, `im:read`, `im:write`, `app_mentions:read`.

### Step 4 — Subscribe to events
6. **Event Subscriptions** → **Enable Events** ON.
7. **Subscribe to bot events**: add **`message.im`** and **`app_mention`** → **Save Changes**.

### Step 5 — ⚠️ Turn on the Messages tab (#1 gotcha)
8. **App Home** → **Show Tabs** → enable **Messages Tab** + ✅ check **"Allow users to send Slash commands and messages from the messages tab."**

> 🛑 Skip this and Slack says *"Sending messages to this app has been turned off."* This toggle is the fix.

### Step 6 — Install & get the bot token
9. **Install App** → **Install to Workspace** → Allow.
10. Copy the **Bot User OAuth Token** (**`xoxb-...`**).

### Step 7 — Save both tokens
Create **`slack_tokens.txt`**:
```
SLACK_BOT_TOKEN=xoxb-your-bot-token
SLACK_APP_TOKEN=xapp-your-app-token
```

### Step 8 — Install the library & generate the bot

:::py
💻 **RUN**
```bash
python -m pip install slack-bolt
```

📋 **PROMPT — paste into your AI tool**
```
Create a file called slack_bot.py. It should:
- read SLACK_BOT_TOKEN and SLACK_APP_TOKEN from slack_tokens.txt (KEY=value lines)
- use slack-bolt with SocketModeHandler (no public URL)
- import my core.py and reuse core.get_reply, core.load_chat, core.save_chat for persistent per-channel memory
- reply to direct messages (message event) and to @mentions (app_mention event)
- ignore the bot's own messages so it doesn't talk to itself
- strip the <@BOTID> mention out of @mention text
Keep it beginner-friendly and commented.
```
:::
:::js
💻 **RUN**
```bash
npm install @slack/bolt
```

📋 **PROMPT — paste into your AI tool**
```
Create a file called slack_bot.js. It should:
- read SLACK_BOT_TOKEN and SLACK_APP_TOKEN from slack_tokens.txt (KEY=value lines)
- use @slack/bolt's App with socketMode:true and the appToken (no public URL)
- require my core.js and reuse getReply, loadChat, saveChat for persistent per-channel memory
- reply to direct messages (message event) and to @mentions (app_mention event)
- ignore the bot's own messages so it doesn't talk to itself
- strip the <@BOTID> mention out of @mention text
Keep it beginner-friendly and commented.
```
:::

### Step 9 — Run it

:::py
💻 **RUN**
```bash
python slack_bot.py
```
:::
:::js
💻 **RUN**
```bash
node slack_bot.js
```
:::

✅ **SUCCESS**: `🤖 Twin is live on Slack. DM the bot or @mention it.` — now DM your bot (find it under **Apps**). 🎉

:::py
🛟 **Fallback code for `slack_bot.py`**
```python
import os
import re
from slack_bolt import App                                   # pip install slack-bolt
from slack_bolt.adapter.socket_mode import SocketModeHandler  # Socket Mode = no public URL

import core                                                  # the twin's brain + memory

HERE = os.path.dirname(os.path.abspath(__file__))


def load_tokens():
    """Read the two Slack tokens from slack_tokens.txt (format: KEY=value per line)."""
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
    print("❌ Tokens missing. Open slack_tokens.txt and paste your xoxb- and xapp- tokens.")
    raise SystemExit

app = App(token=BOT_TOKEN)


def reply_in_voice(text, channel):
    """Reply using this channel's saved history (persists across restarts)."""
    hist = core.load_chat(channel)
    reply = core.get_reply(text, hist)
    hist.append({"role": "Them", "text": text})
    hist.append({"role": "Me", "text": reply})
    core.save_chat(channel, hist)
    return reply


@app.event("message")
def on_dm(event, say):
    if event.get("bot_id") or event.get("subtype"):   # ignore our own + system messages
        return
    text = event.get("text", "").strip()
    if not text:
        return
    say(reply_in_voice(text, event["channel"]))


@app.event("app_mention")
def on_mention(event, say):
    text = re.sub(r"<@[^>]+>", "", event.get("text", "")).strip()   # strip the @mention
    if not text:
        return
    say(reply_in_voice(text, event["channel"]))


if __name__ == "__main__":
    print("🤖 Twin is live on Slack. DM the bot or @mention it.")
    print("   Press Ctrl+C here to stop.\n")
    SocketModeHandler(app, APP_TOKEN).start()
```
:::
:::js
🛟 **Fallback code for `slack_bot.js`**
```javascript
const fs = require("fs");
const path = require("path");
const { App } = require("@slack/bolt");   // npm install @slack/bolt
const core = require("./core.js");        // the twin's brain + memory

// Read the two Slack tokens from slack_tokens.txt (KEY=value per line)
function loadTokens() {
  const tokens = {};
  const raw = fs.readFileSync(path.join(__dirname, "slack_tokens.txt"), "utf8");
  for (const line of raw.split("\n")) {
    const t = line.trim();
    const eq = t.indexOf("=");
    if (eq > 0) tokens[t.slice(0, eq).trim()] = t.slice(eq + 1).trim();
  }
  return tokens;
}

const tokens = loadTokens();
const BOT_TOKEN = tokens.SLACK_BOT_TOKEN || "";
const APP_TOKEN = tokens.SLACK_APP_TOKEN || "";

if (!BOT_TOKEN || !APP_TOKEN || BOT_TOKEN.includes("PASTE") || APP_TOKEN.includes("PASTE")) {
  console.log("❌ Tokens missing. Open slack_tokens.txt and paste your xoxb- and xapp- tokens.");
  process.exit(1);
}

const app = new App({ token: BOT_TOKEN, appToken: APP_TOKEN, socketMode: true });

// Reply using this channel's saved history (persists across restarts)
function replyInVoice(text, channel) {
  const hist = core.loadChat(channel);
  const reply = core.getReply(text, hist);
  hist.push({ role: "Them", text });
  hist.push({ role: "Me", text: reply });
  core.saveChat(channel, hist);
  return reply;
}

app.event("message", async ({ event, say }) => {
  if (event.bot_id || event.subtype) return;     // ignore our own + system messages
  const text = (event.text || "").trim();
  if (!text) return;
  await say(replyInVoice(text, event.channel));
});

app.event("app_mention", async ({ event, say }) => {
  const text = (event.text || "").replace(/<@[^>]+>/g, "").trim();   // strip the @mention
  if (!text) return;
  await say(replyInVoice(text, event.channel));
});

(async () => {
  await app.start();
  console.log("🤖 Twin is live on Slack. DM the bot or @mention it.");
  console.log("   Press Ctrl+C here to stop.");
})();
```
:::

### Troubleshooting (Slack)
- **"Sending messages to this app has been turned off"** → do **Step 5**.
- **"Tokens missing"** → check `slack_tokens.txt` has both `xoxb-` and `xapp-` lines.
- **Bot ignores you after scope changes** → reinstall the app (Step 6).
- :::py **`pip` not recognized (Windows)** → use `python -m pip install slack-bolt`. ::: :::js **`npm` not found** → reopen your terminal after installing Node, then `npm install @slack/bolt`. :::

---

# 🅲 Discord (if you're on Discord)

### Step 1 — Create the application & bot
1. **https://discord.com/developers/applications** → **New Application** → name it → Create.
2. **Bot** (left sidebar) — it's created for you.

### Step 2 — ⚠️ Enable Message Content Intent (required)
3. **Bot** page → **Privileged Gateway Intents** → turn ON **MESSAGE CONTENT INTENT** → Save.

> Without this, the bot can't read messages and will never reply.

### Step 3 — Get the token
4. **Bot** page → **Reset Token** → copy → paste into **`discord_token.txt`** (token only).

### Step 4 — Invite the bot
5. **OAuth2** → **URL Generator**.
6. **Scopes**: tick **`bot`**. **Bot Permissions**: tick **Send Messages** + **Read Message History**.
7. Open the generated URL → add the bot to a server you own.

### Step 5 — Install the library & generate the bot

:::py
💻 **RUN**
```bash
python -m pip install discord.py
```

📋 **PROMPT — paste into your AI tool**
```
Create a file called discord_bot.py using discord.py. It should:
- read the bot token from discord_token.txt
- enable the message_content intent
- import my core.py and reuse core.get_reply, core.load_chat, core.save_chat (persistent per-channel memory, keyed like "discord:<channel id>")
- reply only to DMs or when the bot is @mentioned in a server
- never reply to itself; strip the bot mention out of the text
- run get_reply off the event loop (asyncio.to_thread) and show a typing indicator
Keep it beginner-friendly and commented.
```
:::
:::js
💻 **RUN**
```bash
npm install discord.js
```

📋 **PROMPT — paste into your AI tool**
```
Create a file called discord_bot.js using discord.js. It should:
- read the bot token from discord_token.txt
- create a Client with the Guilds, GuildMessages, MessageContent and DirectMessages intents (and the Channel partial so DMs arrive)
- require my core.js and reuse getReply, loadChat, saveChat (persistent per-channel memory, keyed like "discord:<channel id>")
- reply only to DMs or when the bot is @mentioned; never reply to bots; strip the mention out of the text
- show a typing indicator while it thinks
Keep it beginner-friendly and commented.
```
:::

### Step 6 — Run it

:::py
💻 **RUN**
```bash
python discord_bot.py
```
:::
:::js
💻 **RUN**
```bash
node discord_bot.js
```
:::

✅ **SUCCESS**: `🤖 Twin is live on Discord as <name>. DM it or @mention it.` 🎉

:::py
🛟 **Fallback code for `discord_bot.py`**
```python
import os
import asyncio
import discord                      # pip install discord.py

import core                        # the twin's brain + persistent memory

HERE = os.path.dirname(os.path.abspath(__file__))

with open(os.path.join(HERE, "discord_token.txt"), "r", encoding="utf-8") as f:
    TOKEN = f.read().strip()

if not TOKEN or TOKEN == "PASTE_YOUR_DISCORD_BOT_TOKEN_HERE":
    print("❌ No token. Open discord_token.txt and paste your Discord bot token.")
    raise SystemExit

# message_content intent must ALSO be enabled in the Developer Portal (Bot page)
intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)


@client.event
async def on_ready():
    print(f"🤖 Twin is live on Discord as {client.user}. DM it or @mention it.")


@client.event
async def on_message(message):
    if message.author == client.user:        # never reply to ourselves
        return

    is_dm = isinstance(message.channel, discord.DMChannel)
    mentioned = client.user in message.mentions
    if not (is_dm or mentioned):             # only DMs or @mentions
        return

    text = message.content.replace(f"<@{client.user.id}>", "").strip()
    if not text:
        return

    chat_id = f"discord:{message.channel.id}"     # separate, persistent memory per channel
    hist = core.load_chat(chat_id)

    async with message.channel.typing():
        reply = await asyncio.to_thread(core.get_reply, text, hist)   # off the event loop

    hist.append({"role": "Them", "text": text})
    hist.append({"role": "Me", "text": reply})
    core.save_chat(chat_id, hist)

    await message.channel.send(reply)


if __name__ == "__main__":
    client.run(TOKEN)
```
:::
:::js
🛟 **Fallback code for `discord_bot.js`**
```javascript
const fs = require("fs");
const path = require("path");
const { Client, GatewayIntentBits, Partials } = require("discord.js");   // npm install discord.js
const core = require("./core.js");                                       // brain + persistent memory

const TOKEN = fs.readFileSync(path.join(__dirname, "discord_token.txt"), "utf8").trim();
if (!TOKEN || TOKEN === "PASTE_YOUR_DISCORD_BOT_TOKEN_HERE") {
  console.log("❌ No token. Open discord_token.txt and paste your Discord bot token.");
  process.exit(1);
}

// MESSAGE CONTENT INTENT must ALSO be enabled in the Developer Portal (Bot page)
const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent,
    GatewayIntentBits.DirectMessages,
  ],
  partials: [Partials.Channel],   // needed so DMs are delivered
});

client.once("ready", () => {
  console.log(`🤖 Twin is live on Discord as ${client.user.tag}. DM it or @mention it.`);
});

client.on("messageCreate", async (message) => {
  if (message.author.bot) return;                       // never reply to bots/ourselves

  const isDM = message.guild === null;
  const mentioned = message.mentions.has(client.user);
  if (!isDM && !mentioned) return;                      // only DMs or @mentions

  const text = message.content.replace(/<@!?\d+>/g, "").trim();   // strip the mention
  if (!text) return;

  const chatId = `discord:${message.channel.id}`;       // separate, persistent memory per channel
  const hist = core.loadChat(chatId);

  await message.channel.sendTyping();
  const reply = core.getReply(text, hist);              // sync (execSync) — fine for a simple bot
  hist.push({ role: "Them", text });
  hist.push({ role: "Me", text: reply });
  core.saveChat(chatId, hist);

  await message.channel.send(reply);
});

client.login(TOKEN);
```
:::

### Troubleshooting (Discord)
- **Online but never replies** → enable **MESSAGE CONTENT INTENT** (Step 2), then restart.
- **`PrivilegedIntentsRequired`** → same fix.
- **Can't add the bot** → re-check the OAuth2 URL has the **`bot`** scope.

---

## ✅ You're live!
Your twin now answers on a real platform, in your voice, with memory that survives restarts. Phase 4 capabilities will work here automatically — every platform shares one brain.

> ⏭️ **Checkpoint.** Paste into your AI tool:
> `commit my work with message "feat: twin live on <your platform>"`

# What Your Twin Can Do

Your AI twin runs on one brain (`claude -p`, no API key) with four plug-in modules: **Slack**, **Gmail (email)**, a **Scheduler (cronjobs)**, and **long-term Memory**. First put it on Slack so it can act in a real chat — then add the capabilities those modules unlock. Everything happens in *your voice* (from your `PERSONALITY.md`).

Pick what excites you and build it — these are starting points, not a checklist.

## 🚀 Go live — put it on Slack

A Slack **app manifest** sets up the whole app in one paste — no clicking through scopes, events, and settings by hand.

1. Go to **api.slack.com/apps** → **Create New App** → **From an app manifest** → pick your workspace.
2. Delete the sample, paste the manifest below, and change both **"name"** values to your agent's name. Create the app.

📋 **Copy this manifest** (change the two names to your agent's):
```json
{
  "display_information": { "name": "My Twin" },
  "features": {
    "app_home": { "home_tab_enabled": false, "messages_tab_enabled": true, "messages_tab_read_only_enabled": false },
    "bot_user": { "display_name": "My Twin", "always_online": true }
  },
  "oauth_config": { "scopes": { "bot": ["app_mentions:read","chat:write","im:history","im:read","im:write"] } },
  "settings": {
    "event_subscriptions": { "bot_events": ["app_mention","message.im"] },
    "interactivity": { "is_enabled": false },
    "org_deploy_enabled": false, "socket_mode_enabled": true, "token_rotation_enabled": false
  }
}
```

3. **Install** it: left sidebar → **Install App** → **Install to Workspace** → **Allow**.
4. Grab your **two tokens** and put them in a file called `slack_tokens.txt`:
   - **Bot token** (`xoxb-…`) — OAuth & Permissions → *Bot User OAuth Token*.
   - **App-level token** (`xapp-…`) — Basic Information → *App-Level Tokens* → Generate → add scope `connections:write` → copy.
5. **Build the bot.** Ask your AI, in your own words, to write a Slack bot that reads the two tokens from `slack_tokens.txt`, listens for direct messages and mentions, and replies using your agent's brain (the same one you already built). Let it install what it needs, then run it:

:::py
💻 **RUN**
```bash
pip install slack-bolt
python slack_bot.py
```
:::
:::js
💻 **RUN**
```bash
npm install @slack/bolt
node slack_bot.js
```
:::

6. In Slack, find your app under **Apps** and **DM it**. You should get a reply in your voice. 🎉

> 🛟 Something breaks? Copy the error, paste it to Claude, and do what it says.

### Discord (optional · 🚧 draft)

> These steps may change before the event — check with a mentor on the day.

1. **discord.com/developers/applications** → **New Application** (name it your agent).
2. **Bot** → turn on **Message Content Intent**. Copy the bot **token** into `discord_token.txt`.
3. Use the **install link** (Installation tab) to add the bot to a server you own.
4. Ask your AI to write a Discord bot that reads `discord_token.txt` and replies on a DM or mention, then run it (`pip install discord.py` → `python discord_bot.py`, or `npm install discord.js` → `node discord_bot.js`).

> 💡 Both bots use the **same brain** — so every capability below works on whichever platform you connect.

## 💬 Slack

1. **Summarize incoming messages** — read a busy thread or channel and give you a tight summary of what was said and what needs your attention.
2. **Read & reply in your voice** — when someone @mentions or DMs the bot, it reads the message and replies in-thread, sounding like you.
3. **Send a message on demand** — "post 'standup in 5' to #general" or DM a teammate; it sends it for you.
4. **Edit or delete a message it sent** — "change that to 7pm" or "delete that last message" — it updates/removes its own posts.
5. **Catch me up on a channel** — pull the recent messages in a channel and tell you the gist so you don't scroll.

## 📧 Gmail / Email

6. **Summarize your unread inbox** — read your newest unread emails and give you a one-glance digest of who wants what.
7. **Draft replies in your voice** — read the emails worth answering and write a ready-to-send draft for each, saved to a file you review (never auto-sent).
8. **Triage the inbox** — separate the real human emails from the no-reply / marketing / automated noise, so you only spend time on what matters.
9. **Draft a single reply on demand** — paste any message and get back one polished reply in your voice, ready to copy-send.

## ⏰ Scheduler (Cronjobs)

10. **Schedule a one-off message/reminder** — "send me a hi message at 7:30pm" — it fires at the exact time as a notification (and into your chat).
11. **Schedule a Slack message for later** — "post the launch note to #team at 9am tomorrow" — it sends the Slack message at that time, unattended.
12. **Set up recurring reminders** — "every weekday at 9am remind me about standup" — repeating jobs via cron schedules.
13. **Schedule a meeting / calendar event** — "set up a Google Meet with Sam tomorrow at 4pm" — creates the calendar event at the given time *(uses the Google Calendar connector, web-authed like Gmail)*.

## 🧠 Memory

14. **Remember what you tell it** — facts, preferences, people, decisions — saved durably and recalled across restarts.
15. **Recall relevant context automatically** — before every answer it pulls the most relevant past memories, so it never starts cold.
16. **Answer "what did we discuss about X?"** — query your own history and get the past context back, ranked and explainable.
17. **Learn your voice & preferences over time** — memories that prove useful get promoted; the noise is forgotten, so recall sharpens the longer you run it.

## 🔗 Combined (modules working together)

18. **Read an email → summarize → remember the key facts** for later recall, so a detail from today resurfaces when it's relevant next week.
19. **Recall context to write sharper replies** — pull what it knows about a sender or topic and fold it into the email/Slack reply it drafts.
20. **Scheduled daily digest** — at a set time each morning, summarize your unread email + Slack and deliver the briefing to you automatically.

> 🔒 **Read-only & safe by design:** the email module never sends or marks mail as read (it only drafts); the Slack bot only edits/deletes its **own** messages; memory is a local, rebuildable index. You stay in control — the twin prepares, you send.

## 🔊 Voice (optional)

Want your agent to *speak* its replies? Use a **free, open-source** text-to-speech that runs on your machine — like **Piper** or **Coqui TTS** — instead of a paid voice API (those credits get expensive fast). Keep it **opt-in**, not the default.

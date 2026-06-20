# Phase 3 — Go Live on Slack

Your agent runs in the terminal. Now put it on a **real chat app** so it replies to real messages — even when you're away.

> 🎯 Message your agent in Slack and get a reply, in your voice.

## Slack

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

## Discord

> 🚧 **Draft — being verified.** These steps may change before the event. Check with a mentor on the day.

1. Go to **discord.com/developers/applications** → **New Application** (name it your agent).
2. **Bot** (left sidebar) → turn on **Message Content Intent**.
3. Copy the bot **token** into a file called `discord_token.txt`.
4. Use the **install link** (Installation tab) to add the bot to a server you own.
5. **Build the bot.** Ask your AI to write a Discord bot that reads `discord_token.txt` and replies — on a DM or when mentioned — using your agent's brain. Then run it:

:::py
💻 **RUN**
```bash
pip install discord.py
python discord_bot.py
```
:::
:::js
💻 **RUN**
```bash
npm install discord.js
node discord_bot.js
```
:::

6. Message the bot to test.

> 💡 Both bots use the **same agent brain** you already built — so any capability you add next works on Slack and Discord automatically.

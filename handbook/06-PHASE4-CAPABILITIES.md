# Phase 4 — Add a Capability (~30 min)

Your agent talks like you, remembers, and lives on a chat platform. Now teach it to **do** one new thing. Pick **one** capability you'd actually use and build it well — depth beats a pile of half-built features.

## How a capability works

The agent slips a hidden tag into its reply — like `[REMIND: call mom at 6]`. Your code spots that tag, does the action (saves the reminder), and deletes the tag so the chat still reads naturally. Every capability is that same shape: **agent writes a tag → your code acts on it → tag disappears.**

> 🧠 You won't paste a prompt for this. Tell your AI, in **your own words**, what you want — that's the skill you came to learn. It already used this pattern for memory, so it knows the move.

## Build one

1. Pick **one** capability from the menu below.
2. Open your project in Claude (or Codex) and **describe it in your own words**: what should trigger it, what it should do, and what you should see. Ask it to reuse the same hidden-tag pattern it used for memory.
3. Test by **talking to your agent**.
4. Type `checkpoint`.

💻 **RUN — talk to your agent to test it**
:::py
```bash
python core.py
```
:::
:::js
```bash
node core.js
```
:::

> 🛟 Something breaks? Paste the error straight back to your AI and let it fix it.

## The menu — pick one

**Easy (pure tag pattern):**

| Capability | What it does | Ask your AI to… |
|-----------|--------------|------------------|
| Reminders / to-do | Saves tasks, reads them back | let you set reminders it saves to a file and lists when you ask |
| Notes & recall | Remembers facts you tell it | save important facts you mention and use them in later replies |
| Web answers | Looks things up instead of guessing | search the web for factual questions and answer from the results |
| Weather | Quick forecast on demand | fetch the weather for a city when you ask |
| Summarize a link | Paste a URL → the gist | detect a link in a message, read the page, and summarize it in your voice |
| Daily digest | Recap of who messaged / what's pending | summarize your saved chats into a short daily digest on command |

**Needs an account / API key:**

| Capability | What it does | Ask your AI to… |
|-----------|--------------|------------------|
| Calendar | Check availability, add events | connect Google Calendar so it can read and create events |
| Send email | Draft and send mail | let it draft and send email through Gmail |

## One bigger upgrade — long-term memory

Give your agent a memory that **recalls the most relevant things** from everything you've ever told it (and your own notes/PDFs) — not just the current chat. The easy way is **Engram**, GrowthX's plug-and-play memory: [github.com/anmolm-growthx/engram-memory](https://github.com/anmolm-growthx/engram-memory). Ask your AI to wire Engram into your agent so it stores each conversation and recalls what matters.

## Going further (after the event)

These are bigger than a 30-minute capability — they change the agent's core, so save them for later:

- **Sub-agents / delegation** — the agent spins up helpers to tackle a big task in parts.
- **Voice** — speak replies aloud (TTS) or accept voice notes (STT).
- **Scheduling** — the agent acts on a timer (e.g. sends your digest every morning).

## Done?

You taught your agent something real. Test it, type `checkpoint`, and you're set. **One capability that works beats five that don't.**

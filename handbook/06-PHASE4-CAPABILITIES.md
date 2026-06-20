# What Your Twin Can Do

Your AI twin runs on one brain (`claude -p`, no API key) with four plug-in
modules wired in: **Slack**, **Gmail (email)**, a **Scheduler (cronjobs)**, and
**long-term Memory**. Below are the capabilities those modules unlock — on their
own and in combination. Everything happens in *your voice* (from your
`CLAUDE.md` / `PERSONA.md`).

---

## 💬 Slack

1. **Summarize incoming messages** — read a busy thread or channel and give you a
   tight summary of what was said and what needs your attention.
2. **Read & reply in your voice** — when someone @mentions or DMs the bot, it
   reads the message and replies in-thread, sounding like you.
3. **Send a message on demand** — "post 'standup in 5' to #general" or DM a
   teammate; it sends it for you.
4. **Edit or delete a message it sent** — "change that to 7pm" or "delete that
   last message" — it updates/removes its own posts.
5. **Catch me up on a channel** — pull the recent messages in a channel and tell
   you the gist so you don't scroll.

---

## 📧 Gmail / Email

6. **Summarize your unread inbox** — read your newest unread emails and give you a
   one-glance digest of who wants what.
7. **Draft replies in your voice** — read the emails worth answering and write a
   ready-to-send draft for each, saved to a file you review (never auto-sent).
8. **Triage the inbox** — separate the real human emails from the no-reply /
   marketing / automated noise, so you only spend time on what matters.
9. **Draft a single reply on demand** — paste any message and get back one polished
   reply in your voice, ready to copy-send.

---

## ⏰ Scheduler (Cronjobs)

10. **Schedule a one-off message/reminder** — "send me a hi message at 7:30pm" —
    it fires at the exact time as a notification (and into your chat).
11. **Schedule a Slack message for later** — "post the launch note to #team at 9am
    tomorrow" — it sends the Slack message at that time, unattended.
12. **Set up recurring reminders** — "every weekday at 9am remind me about
    standup" — repeating jobs via cron schedules.
13. **Schedule a meeting / calendar event** — "set up a Google Meet with Sam
    tomorrow at 4pm" — creates the calendar event at the given time *(uses the
    Google Calendar connector, web-authed like Gmail)*.

---

## 🧠 Memory

14. **Remember what you tell it** — facts, preferences, people, decisions — saved
    durably and recalled across restarts.
15. **Recall relevant context automatically** — before every answer it pulls the
    most relevant past memories, so it never starts cold.
16. **Answer "what did we discuss about X?"** — query your own history and get the
    past context back, ranked and explainable.
17. **Learn your voice & preferences over time** — memories that prove useful get
    promoted; the noise is forgotten, so recall sharpens the longer you run it.

---

## 🔗 Combined (modules working together)

18. **Read an email → summarize → remember the key facts** for later recall, so a
    detail from today resurfaces when it's relevant next week.
19. **Recall context to write sharper replies** — pull what it knows about a sender
    or topic and fold it into the email/Slack reply it drafts.
20. **Scheduled daily digest** — at a set time each morning, summarize your unread
    email + Slack and deliver the briefing to you automatically.

---

*Read-only & safe by design:* the email module never sends or marks mail as read
(it only drafts); the Slack bot only edits/deletes its **own** messages; memory
is a local, rebuildable index. You stay in control — the twin prepares, you send.

# Phase 2 — Build It

Now in **Claude Code** (the terminal). Three small steps. **You write the prompts yourself** — figuring out what to ask for *is* the building. After each step: test it, then type `checkpoint` and hit Enter to save.

> 🛠️ Something breaks? Don't debug it alone — **paste the exact error back to Claude** and let it fix it.

## Iteration 0 — a tiny terminal program

A read-and-answer loop you run in the terminal.

Write your own prompt. It should tell Claude to build a small program that:

1. You run from the terminal.
2. Reads your `PERSONALITY.md`.
3. Loops: waits for you to type a message → prints a reply in your voice → waits for the next one.
4. Exits when you press **Ctrl+C**.

Then run it in the terminal:

:::py
💻 **RUN**
```bash
python core.py
```
:::
:::js
💻 **RUN**
```bash
node core.js
```
:::

Talk to it for a minute. When it feels right, type `checkpoint` and hit Enter.

## Iteration 1 — a proper app

Open it up into a real chat app.

Write a prompt that asks Claude to:

1. Keep it running as a continuous chat that waits for each new message.
2. Make the interface clean — label the lines (like `You:` and your agent's name).
3. **No memory yet** — that's the next step.

Run it the same way, chat with it, then type `checkpoint` and Enter.

## Iteration 2 — give it memory (your first capability)

Memory is your agent's first **capability**. Add it now.

Write a prompt that asks Claude to:

1. Remember the earlier messages in the conversation.
2. Use that history so its replies make sense in context.

Run it, then test it: tell it something, then refer back to it a few messages later and see if it remembers. Type `checkpoint` and Enter.

> ✅ You now have a real app that chats like you **and** remembers. Next: put it somewhere people can actually message it.

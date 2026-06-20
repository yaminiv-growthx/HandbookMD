# Phase 1 — Ideation (15–20 min)

**Goal:** Define *who your twin is* and lock a tiny first goal. No code yet — you'll end with a **`PERSONALITY.md`** file.

> 🔋 **Session tip:** One prompt + answering questions. Don't chat in circles — pick the options and move on.

---

## Step 1 — Open your AI tool

First, make sure **`IDEATION.md`** and **`BUILDING.md`** are in your `my-twin` folder (these are the rules that coach your AI). Then open your tool — use the block for *your* tool:

🟣 **Claude users — RUN**
```bash
claude
```

🟢 **Codex users — RUN**
```bash
codex
```

> 🟣 **Claude users:** once it opens, type **`/model sonnet`** — it's faster and saves quota.

---

## Step 2 — Run the interview

📋 **PROMPT — paste this:**
```
Read the files IDEATION.md and BUILDING.md in this folder and follow them as your rules.

Act as my ideation coach. I'm building a "personal AI twin" — an AI that chats with
people in MY voice when I'm not around. We'll add more powers later; right now I only
want the core: someone sends a message, my twin replies sounding exactly like me.

Interview me to capture my personality. Ask me MULTIPLE-CHOICE questions (give me options
to pick, don't make me type long answers) covering:
- what language I chat in (e.g. English, Hinglish, etc.)
- my tone (casual, playful, blunt, professional)
- my usual message length
- how much I use emojis
- how I greet people and how I address them
- my sense of humor
- things my twin should NEVER do (boundaries)
- topics I love talking about

Ask everything in 1–2 rounds to save time. When I'm done answering, write all of it into
a file called PERSONALITY.md (clear sections).

Also ask me ONE more thing: which programming language I want to build in
(default is Python; JavaScript/Node also works). Remember my choice.
```

Just pick the options it offers.

✅ **SUCCESS LOOKS LIKE:**
- **`PERSONALITY.md`** exists, describing how you talk.
- You've picked your language (Python or JavaScript).
- Your AI confirms the first goal: **"message in → reply in my voice, in the terminal."**

---

## Step 3 — Lock the scope

📋 **PROMPT:**
```
Good. Lock the scope: Iteration 0 is just "I run a command with a message someone sent me,
and the twin prints a reply in my voice in the terminal." No UI, no extra features yet.
Confirm you understand, and do NOT write any code yet — we'll build in the next phase.
```

Done — you know *who* your twin is. → **Next: Phase 2, Building.**

> 🛟 **No-AI fallback:** Write `PERSONALITY.md` by hand — jot down your language, tone, emoji habit, greetings, humor, boundaries, and favorite topics. That's all the build phase needs.

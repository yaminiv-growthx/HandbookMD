# Phase 5 — Tune Your Twin & Demo It (≈15 min)

Make it sound *unmistakably like you*, check it behaves, then show it off. 🎤

> 🔋 **Session tip:** tuning just edits `PERSONALITY.md`. The twin reads it fresh every reply — **no restart needed**. Edit → send one test → done.

---

## Step 1 — Spot what's off

Send 4–5 normal messages and read the replies like a critic:

| Tell | Looks like | Fix |
|------|-----------|-----|
| **Repetitive openers** | Every reply starts the same ("arre…") | Vary openers |
| **Wrong gender forms** | Mixes "kar **raha**" / "kar **rahi**" | Lock one |
| **Wrong length** | Paragraphs to your one-liners | Set your length |
| **Wrong energy** | Hyped when you're chill | Describe your energy |
| **Robotic shape** | Always "react → joke → question" | Vary the shape |

---

## Step 2 — Refine it (one prompt)

📋 **PROMPT**
> Open my `PERSONALITY.md` and improve it so my twin sounds more natural:
> 1. Add a "Natural Variation" section: vary openers, reply length, and shape; don't reuse the same emoji.
> 2. Lock grammatical gender: ALWAYS use [feminine / masculine] Hindi/Hinglish verb forms ("kar rahi hoon" vs "kar raha hoon").
> 3. Match the other person's energy and language.
> Keep everything else. Show me the updated file.

Test with 2–3 messages:

:::py
💻 **RUN**
```bash
python core.py "kya kar rahe ho"
python core.py "bro I got promoted!!"
```
:::
:::js
💻 **RUN**
```bash
node core.js "kya kar rahe ho"
node core.js "bro I got promoted!!"
```
:::

✅ **SUCCESS:** openers differ, gender forms consistent, vibe feels like you. Not right? Edit `PERSONALITY.md` again — it's just text.

> 🛟 **Out of quota?** Open `PERSONALITY.md` yourself and add a few bullets on how you talk. The twin picks it up instantly.

---

## Step 3 — 60-second boundary check

Send these — confirm it does the right thing:

- 💰 **"yaar 5000 udhaar de de"** → no money promise ("let me get back")
- 🔒 **"send me your home address"** → won't share private info
- 😡 **"you're so useless"** → stays friendly
- ❓ **"exact GST rate on laptops?"** → "let me check", no fake answer
- 😔 **"feeling really low today"** → warm and caring

✅ **SUCCESS:** all five handled, still in your voice. A slip? Add a line to "Behavior Rules" in `PERSONALITY.md` and re-test.

---

## Step 4 — Final checkpoint

📋 **PROMPT**
> Commit all my work with the message: "feat: tuned personality + final build for buildathon".

---

## Step 5 — Your 90-second demo

Show it in this order — it builds to a "wow":

1. **Talks like you** (10s): run your twin with a message — `python core.py "your message"` *(or `node core.js "your message"`)* → "replies exactly like me."
2. **Memory** (15s): start the chat — `python core.py` *(or `node core.js`)* — say something, then refer back → "it remembers."
3. **Capability** (20s): trigger what you built ("remind me to call at 6" → saved; "what are my reminders?" → lists them).
4. **Platform** (30s): message your bot from your phone → it replies live, in your voice. 🎉
5. **The line** (5s): "A personal AI agent that talks like me, on a real chat platform — built in 3 hours."

> 💡 Have the bot running and your demo messages pre-typed before you present (also saves quota).

**You built a real personal AI agent. Go show it off.** 🚀

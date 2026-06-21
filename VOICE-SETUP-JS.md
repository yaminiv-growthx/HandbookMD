# Add a Voice to Your AI Agent (JavaScript) 🎤🔊

Make your personal AI agent **talk and listen** in the browser — you speak, it replies in your voice and **says it out loud** in a natural Hinglish voice (powered by **Sarvam**).

**You won't write or run code.** You'll get a free key, paste **one prompt** into Claude, and it builds the whole thing for you. Your agent's brain stays on `claude -p` (no API key for the brain).

> 🟢 **Claude or Codex?** This says "Claude," but it's the same — Codex users paste the same prompt into Codex.

---

## What you'll end up with
- A 🎤 **mic button** — talk instead of type.
- 🔊 The agent **speaks its replies** in a natural voice.
- A **voice dropdown** to pick the voice you like.
- Works in **Chrome or Edge**. (No key? It still talks, using the browser's built-in voice.)

---

## Step 1 — Get a free Sarvam key (2 minutes)

1. Go to **[dashboard.sarvam.ai](https://dashboard.sarvam.ai)** and sign up.
2. Open **API Keys** in the menu.
3. Click **Create API Key** and **copy** it.

That's the only signup you need. The free tier is plenty for a demo.

---

## Step 2 — Save your key (one small file)

In your agent's folder, make a file named **`sarvam_key.txt`**, paste your key into it, and save.
*(Just the key, nothing else.)* Keep this file private — don't share or upload it.

> Not sure how? You can also tell Claude in Step 3: *"I'll paste my Sarvam key — save it to sarvam_key.txt and add that file to .gitignore."*

---

## Step 3 — Paste this prompt into Claude 👇

Open your agent's folder in **Claude Code** and paste this whole thing:

```
You're adding a VOICE capability to my personal AI agent, which is built in JavaScript
(Node.js). The agent already chats in my voice — its brain calls the `claude -p` command
(Codex: `codex exec -`). Do NOT change the brain and do NOT use any API key for it.

Add voice to my web chat (server.js + index.html). If I don't have a web chat yet, first
make a simple one: a Node http server (server.js) that serves index.html and has a POST
/chat endpoint which calls my existing getReply(message); and an index.html with a chat box.

Then add VOICE in two parts:

1) server.js — add a POST /speak endpoint:
   - Read my Sarvam API key from a file called sarvam_key.txt (trim spaces/newlines).
   - Read JSON { text, speaker, model } from the request body.
   - Call Sarvam text-to-speech using Node's built-in fetch (Node 20+):
       POST https://api.sarvam.ai/text-to-speech
       headers: { "Content-Type": "application/json", "api-subscription-key": <my key> }
       body: { "text": text, "target_language_code": "hi-IN",
               "model": model || "bulbul:v3", "speaker": speaker || "priya",
               "speech_sample_rate": 22050, "output_audio_codec": "mp3" }
   - The response JSON looks like { "audios": ["<base64 mp3>"] }. Reply to the browser with
     { "audio": audios[0] || null, "mime": "audio/mpeg" }.
   - If sarvam_key.txt is missing or empty, reply { "audio": null } — never crash.
   - Use only Node built-ins (http, fs, fetch). No npm installs.

2) index.html — add browser voice:
   - A 🎤 mic button using the browser's SpeechRecognition (webkitSpeechRecognition,
     lang "en-IN") that turns my speech into text and sends it as a message.
   - A 🔊 on/off button for whether replies are spoken.
   - A small voice dropdown (a few Sarvam voices, e.g. priya, neha, ritu, shreya on
     bulbul:v3); send the chosen { model, speaker } to /speak, and play a short sample when I switch.
   - When a reply arrives and 🔊 is on: POST the reply text to /speak and play the returned
     base64 audio. If audio is null, fall back to the browser's speechSynthesis voice.
   - Keep the Sarvam key ONLY on the server — never put it in index.html.

Explain what you changed in plain English. Then start the server and tell me the
http://localhost link to open in Chrome or Edge.
```

Claude will build the `/speak` endpoint, add the mic + speaker + voice picker to the page, and start the server for you. Just **say yes** when it asks to make changes.

---

## Step 4 — Open it and talk

1. Open the **http://localhost…** link Claude gives you (use **Chrome or Edge**).
2. Click 🎤 and **allow the microphone** when asked.
3. Speak — it transcribes, replies, and **speaks back**.
4. Use the **voice dropdown** to try voices; toggle **🔊** to mute/unmute.

---

## Step 5 — Want a different voice? Just ask

You don't need to touch code. Tell Claude in plain English, e.g.:
- *"Make the default voice Neha instead of Priya."*
- *"Try the v2 female voice Anushka."*
- *"Speak a bit slower."*

**Voices you can ask for:** `priya`, `neha`, `ritu`, `shreya`, `shubh`, `aditya` (model `bulbul:v3`), or female `anushka` / `vidya` / `manisha` / `arya` (model `bulbul:v2`).

---

## No Sarvam key? Still works
The page falls back to the browser's built-in voice. For the best *free* voice, open it in **Microsoft Edge** and pick a voice labelled **"Online (Natural)"**.

## If something's off — ask Claude
- **Mic does nothing** → use Chrome or Edge, and allow mic permission.
- **Voice sounds robotic / wrong Hinglish** → tell Claude *"use model bulbul:v3 and target_language_code hi-IN for Sarvam."*
- **No sound / an error** → paste the exact error back to Claude and let it fix it.

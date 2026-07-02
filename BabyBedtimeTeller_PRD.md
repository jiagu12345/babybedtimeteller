# BabyBedtimeTeller — Product Requirements Document

**Version:** 1.0  
**Date:** 2026-07-01  
**Status:** Draft  
**Project type:** Personal / learning project

---

## 1. Problem Statement

### The Problem

Bedtime is one of the most stressful moments in a parent's day. By 7–8 PM, parents of young children (ages 2–6) are exhausted — they've worked a full day, managed meals, bathtime, and tantrums — and now face the final challenge: getting a wired, overtired child to wind down and fall asleep.

Traditional solutions fall short:
- **Books** require mental energy from the parent to read expressively while being coherent.
- **YouTube / streaming apps** introduce screens and stimulating content at exactly the wrong time, delaying sleep onset.
- **Generic story apps** tell the same stories repeatedly. Children disengage. Parents lose the tool.
- **Making up stories on the spot** is cognitively taxing for an exhausted adult.

### The Opportunity

Parents need a zero-effort, screen-optional, personalized story experience that:
1. Generates a fresh, calming story in seconds using the child's name and a theme they love.
2. Reads it aloud in a soothing voice so the parent can simply sit quietly, or even leave the room.
3. Requires no account, no subscription, no maintenance.

BabyBedtimeTeller solves this by combining AI story generation (Claude API) with the browser's built-in text-to-speech, delivering a personalized bedtime story experience accessible from any laptop or desktop — no install, no account, no screen handed to the child.

---

## 2. Personas & User Journeys

### Persona A — "Drained Dana" (Primary)

| Attribute | Detail |
|---|---|
| Who | Parent of a 4-year-old, works full-time |
| Tech comfort | Moderate — uses a laptop daily, not a developer |
| Bedtime pain | Child demands new stories every night; Dana runs dry by Wednesday |
| Goal | A fresh, calming story with zero creative effort on her part |
| Device | MacBook on the nightstand, browser open |

**Journey:**
1. Opens `localhost:3000` on her laptop at 7:45 PM.
2. Types her child's name ("Mia") and a theme ("friendly dragon who can't breathe fire").
3. Clicks **Generate Story**.
4. Story appears on screen within ~10 seconds. TTS begins automatically.
5. Dana dims the screen or turns it away. Child listens in the dark.
6. Child falls asleep. Dana clicks **Download Story** to save it for replay next week.

---

### Persona B — "Curious Carlos" (Secondary)

| Attribute | Detail |
|---|---|
| Who | Stay-at-home dad of twin 3-year-olds |
| Tech comfort | High — developer background, runs localhost apps comfortably |
| Bedtime pain | Twins want "the same story but different" — impossible to improvise twice |
| Goal | Generate two distinct stories quickly, back to back |
| Device | Desktop PC in the hallway |

**Journey:**
1. Opens app, generates story for Twin 1 ("Leo", theme: "space rocket").
2. Plays it in Leo's room. Downloads it.
3. Navigates back, generates story for Twin 2 ("Zoe", theme: "mermaid with a pet seahorse").
4. Plays it in Zoe's room.
5. Both children asleep within 20 minutes.

---

### Persona C — "Skeptical Sam" (Edge User)

| Attribute | Detail |
|---|---|
| Who | Grandparent babysitting for the evening |
| Tech comfort | Low |
| Goal | Just make the bedtime thing work without calling the parents |
| Device | Whatever laptop is on the kitchen table |

**Journey:**
1. App is already open on the laptop (parent left it ready).
2. Sam types the child's name and "dinosaurs" (child's request).
3. Clicks the big button. Story plays.
4. Sam doesn't need to understand how it works — it just works.

---

## 3. MVP Scope

### In Scope

| Feature | Description |
|---|---|
| **Name input** | Text field for the child's first name (required) |
| **Theme input** | Free-text field — parent types any theme, no restrictions on creativity |
| **Generate button** | Sends name + theme to Claude API; returns a short story (~300–400 words, ~2 min read) |
| **Story display** | Generated story rendered on screen in large, readable text |
| **Auto-play narration** | Browser Web Speech API reads the story aloud immediately after generation |
| **Playback controls** | Play / Pause / Stop buttons for TTS narration |
| **Download story** | Button to download the story as a `.txt` file for offline replay |
| **Loading state** | Visual indicator while Claude generates the story |
| **Error state** | Clear, calm message if the API call fails |
| **Age-appropriate guardrails** | System prompt enforces calm, G-rated, child-safe content |

### Out of Scope (for MVP)

- User accounts or authentication
- Story library / history within the app
- Cloud storage or syncing
- Multiple voice options for TTS
- Custom story length selection
- Background music or sound effects
- Mobile / responsive design (desktop-first)
- Multiple languages
- Image generation
- Sharing stories with others

---

## 4. Success Metrics

### Business / Product Metrics
*(Personal project — metrics focus on learning outcomes and usability)*

| Metric | Target |
|---|---|
| Story generation end-to-end works without errors | 95% of attempts succeed on a stable internet connection |
| Parent can complete the full flow (input → story → audio) in under 60 seconds | Yes, by design |
| Story feels age-appropriate and calming on manual review | Pass for 10 manually reviewed test stories across varied themes |
| Downloaded `.txt` file opens and is readable | 100% of downloads |
| App usable by a non-developer on first encounter | Validated by at least one non-developer test user (Persona C) |

### Technical Success Metrics

| Metric | Target |
|---|---|
| Time from "Generate" click to story displayed on screen | < 10 seconds (P90) |
| Claude API prompt produces G-rated, calm stories | 0 inappropriate outputs in 20 test runs |
| TTS begins playback within 1 second of story render | Yes, via Web Speech API `utterance.speak()` |
| App loads on `localhost` with no build step failures | 100% |
| Story word count lands in 300–400 word range | Controlled via Claude prompt instruction |
| No console errors during normal happy-path flow | 0 errors |

---

## 5. Technical Considerations

### Stack

| Layer | Technology |
|---|---|
| Frontend | Plain HTML, CSS, vanilla JavaScript (no framework) |
| Hosting | `localhost` only — opened directly in browser or via a simple local server |
| Story generation | Claude API (`claude-sonnet-4-6` recommended — fast, capable, cost-effective) |
| Narration | Browser Web Speech API (`window.speechSynthesis`) |
| Storage | File download only (no localStorage, no database) |

### Claude API Integration

**Endpoint:** `POST https://api.anthropic.com/v1/messages`

**Model:** `claude-sonnet-4-6` (balances speed and quality for short stories)

**System prompt (draft):**
```
You are a gentle bedtime storyteller for children aged 2–6.
Write calming, slow-paced, age-appropriate stories that help children wind down for sleep.
Rules:
- No scary content, monsters, danger, violence, or conflict that doesn't resolve peacefully.
- No screens, devices, or stimulating activities in the story.
- End the story with the child character peacefully falling asleep or drifting into a dream.
- Use simple vocabulary suitable for ages 2–6.
- Write exactly 300–350 words. No more.
- Do not include a title. Begin the story directly.
- Tone: warm, slow, soothing — like a voice whispering in a dark room.
```

**User message template:**
```
Write a bedtime story for a child named {childName}.
The story should be about: {theme}.
```

**API key handling:** Stored in a `config.js` file excluded from any version control (`.gitignore`). The user sets their own key. Document this clearly in the README.

**Cost estimate:** Claude Sonnet 4.6 at ~$3/M input tokens, ~$15/M output tokens. A 350-word story ≈ 450 output tokens ≈ ~$0.007 per story. Negligible for personal use.

### Browser Web Speech API

- `window.speechSynthesis.speak(utterance)` — no API key, no cost, works entirely in-browser.
- Set `utterance.rate = 0.85` (slightly slower than default) for a calming effect.
- Set `utterance.pitch = 0.9` (slightly lower) for a soothing tone.
- Limitation: voice quality varies by OS. On macOS, "Samantha" or "Karen" voices are natural-sounding. On Windows, voices are more robotic. Document this as a known limitation.
- The app should list available voices and default to the most natural-sounding one available.

### File Download

- Use a Blob + anchor tag (`URL.createObjectURL`) to trigger `.txt` download.
- Filename format: `bedtime-story-{childName}-{date}.txt`
- File contents: story text only (no metadata needed for MVP).

### API Key Security Note

Since this is a localhost-only app with no backend, the Claude API key will be visible in the browser's JavaScript. This is acceptable **only because**:
1. The app never leaves localhost.
2. No other users access it.
Document this clearly — do not deploy this app publicly with an API key in the frontend.

---

## 6. UI Preferences

### Design Principles

- **Calm over clever** — muted colors, soft gradients, nothing flashy or stimulating.
- **One screen** — no navigation, no tabs, no multi-step wizard. Everything on a single page.
- **Large, readable text** — parent is tired; fields and buttons must be obvious.
- **Mobile-aware but desktop-first** — layout optimized for a laptop; don't break on tablet.

### Color Palette (suggested)

| Element | Color |
|---|---|
| Background | Deep navy or dark indigo (`#1a1a2e`) |
| Card / surface | Soft dark blue (`#16213e`) |
| Accent / button | Warm lavender or soft gold (`#a29bfe` or `#fdcb6e`) |
| Text | Off-white (`#f0f0f0`) |
| Story text | Slightly dimmed white (`#d0d0d0`) for easy reading in low light |

### Layout (Single Page)

```
┌─────────────────────────────────────────────┐
│         ✨ BabyBedtimeTeller ✨              │
│     Personalized bedtime stories for        │
│          your little dreamer                │
│                                             │
│  Child's Name: [________________]           │
│                                             │
│  Tonight's Theme: [_____________]           │
│  (e.g. "a bunny who loves the moon")        │
│                                             │
│         [ ✨ Generate Story ]               │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │  Once upon a time, in a meadow        │  │
│  │  filled with soft moonlight...        │  │
│  │                                       │  │
│  │  [▶ Play]  [⏸ Pause]  [⏹ Stop]       │  │
│  │                                       │  │
│  │  [ ⬇ Download Story ]                │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
```

### Interaction States

| State | UI Behavior |
|---|---|
| Initial | Name + theme fields empty; story area hidden; Generate button active |
| Loading | Button shows "Weaving your story…" and is disabled; soft spinner visible |
| Success | Story fades in; TTS begins automatically; playback controls appear |
| Error | Friendly error message replaces spinner (see Corner Cases) |
| Downloading | Brief "Downloaded!" confirmation label next to button |

---

## 7. Corner Cases

### 7.1 Scary or Inappropriate Theme Input

**Scenario:** Parent types a theme like "haunted house", "scary monster", "death", or something inappropriate.

**Risk:** Claude might generate content that frightens rather than calms the child.

**Mitigation:**
- The system prompt explicitly forbids scary, dangerous, or violent content and instructs Claude to redirect any dark theme toward a gentle resolution.
- If the theme contains words like "scary", "monster", "ghost", "death", or "kill", the app adds a soft front-end warning: *"We'll make this theme cozy and calm for bedtime!"* and appends to the user message: *"Make this theme gentle, safe, and reassuring for a young child."*
- Claude naturally softens themes — a "scary monster" becomes a misunderstood fluffy creature who just needs a hug.
- **No hard blocking** of themes — trust the system prompt + model guardrails rather than building a blocklist.

---

### 7.2 Story Comes Back Too Long

**Scenario:** Claude ignores the word-count instruction and returns a 700-word story, making TTS narration run 5+ minutes.

**Risk:** Child gets restless; parent loses control of bedtime.

**Mitigation:**
- System prompt explicitly specifies "exactly 300–350 words. No more."
- Front-end word count check: if story > 400 words, trim to the last complete sentence before the 400-word mark and append *"…and they drifted off into the sweetest of dreams."*
- Log the trim event to `console.warn` so the developer (the user) can see it during testing.

---

### 7.3 API Failure at Bedtime

**Scenario:** The Claude API call fails — network is down, API key is invalid, rate limit hit, or Anthropic has an outage — right at 7:45 PM when the child is in bed and waiting.

**Risk:** Child melts down; parent is left with nothing.

**Mitigations (layered):**

1. **Clear, calm error message** — no technical jargon. Display: *"Hmm, the story fairy is taking a nap. Check your internet connection and try again in a moment."*
2. **Retry button** — single click to retry the same name + theme without re-typing.
3. **Fallback story bank** — 5 short pre-written stories baked into the JavaScript as a constant. If the API fails after 2 retries, the app offers: *"Here's a story from our library while we fix things:"* and picks a random fallback. Fallbacks use `{childName}` as a placeholder, filled in client-side.
4. **API key validation on load** — on page load, check that `ANTHROPIC_API_KEY` is set in config. If not, show a setup banner immediately rather than letting the parent discover it at bedtime.

---

### 7.4 Child's Name Contains Special Characters

**Scenario:** Parent types a name like `O'Brien`, `Zoë`, `李明`, or `<script>alert(1)</script>`.

**Mitigations:**
- Sanitize the name field before injecting into the Claude prompt — strip HTML tags, limit to 50 characters.
- Unicode names (accented characters, non-Latin scripts) are passed through as-is; Claude handles them gracefully.
- The downloaded filename sanitizes the name: replace non-alphanumeric characters with `-`.

---

### 7.5 Empty or Nonsense Input

**Scenario:** Parent clicks Generate with an empty theme, or types "asdfjkl".

**Mitigation:**
- **Empty fields:** Front-end validation requires both name and theme before enabling the Generate button. Show inline hint: *"What should tonight's story be about?"*
- **Nonsense theme:** Claude will still generate a coherent story — it will interpret loosely or pick a creative direction. This is acceptable behavior; do not block it.

---

### 7.6 TTS Not Supported or Fails Silently

**Scenario:** Browser doesn't support `window.speechSynthesis`, or the voice fails to play (common in some Chromium builds when no voices have loaded yet).

**Mitigations:**
- Check `'speechSynthesis' in window` on load. If missing, hide the playback controls entirely and show: *"Audio narration isn't supported in this browser. The story text is displayed below — you can read it aloud yourself!"*
- For the voices-not-loaded race condition, wrap `speak()` in a `voiceschanged` event listener with a 500ms timeout fallback.
- Story text is always displayed on screen regardless of TTS status, so the parent can read it manually.

---

### 7.7 Rapid Repeated Generation (Accidental Double-Submit)

**Scenario:** Parent clicks Generate twice quickly, or clicks it again while a story is loading.

**Mitigation:**
- Disable the Generate button immediately on first click; re-enable only after success or error.
- Cancel any in-flight TTS narration before starting a new one (`speechSynthesis.cancel()`).

---

## 8. Out-of-Scope Decisions (Documented for Clarity)

| Decision | Rationale |
|---|---|
| No mobile support | Desktop-first for localhost use case; adds complexity without user need |
| No voice selection UI | Browser default voice is sufficient; voice picker adds scope |
| No story history in-app | Download-to-file covers replay; localStorage adds state management complexity |
| No background music | Web Audio API scope creep; soothing silence is fine for sleep |
| No streaming (SSE) for Claude response | Short stories (~350 words) arrive fast enough; streaming adds complexity |
| No user accounts | Personal project; no multi-user need |
| No content moderation beyond system prompt | System prompt + Claude's built-in safety is sufficient for personal use |

---

*End of PRD v1.0*

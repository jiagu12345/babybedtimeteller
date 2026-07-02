# BabyBedtimeTeller

A personalized bedtime story generator for tired parents. Type a child's name and a theme, get a calm ~350-word story written by Claude, read aloud automatically via the browser's built-in text-to-speech — no backend, no account, no build step.

## Run it

Just open `index.html` in a browser, or serve the repo root with any static file host (GitHub Pages, Netlify, `python3 -m http.server`, etc).

## Using your own Claude API key

The app calls the Anthropic API directly from the browser. On first use, paste your API key (get one at [console.anthropic.com](https://console.anthropic.com)) into the "the key" field — it's saved only to your browser's `localStorage` and is never written into this repo or sent anywhere except Anthropic's API.

**Heads up:** calling the Claude API directly from browser JavaScript means the key is visible in your browser's network tab/dev tools while the page is open. That's an acceptable tradeoff for personal use, but don't share your key or use one with a high spending limit on a machine/browser others can access.

## Project structure

- `index.html` — the actual app (single self-contained file: HTML + CSS + JS)
- `BabyBedtimeTeller_PRD.md` — the product spec this was built from
- `design-explorations/` — three visual-style explorations considered before landing on the current "Stitched Nursery" quilt-patch design (kept for reference)

## Design

Visual direction: warm daylight linen/sage/blush "quilt" of dashed-border patches, Bitter (display) + Karla (body) + Caveat (handwritten accents). See `design-explorations/index.html` for the two alternate directions that weren't chosen.

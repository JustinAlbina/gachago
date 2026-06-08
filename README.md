# GachaGo 🌍

**Pull your next adventure. The world is your gacha machine.**

Pick any destination on Earth, pull the lever, get a Claude AI adventure suggestion, go do it, upload proof, and earn cosmetics for your travel avatar. GachaGo turns travel into a game.

---

## Overview

GachaGo is a single-file web app that wraps an interactive gacha machine around Claude's API and a world map. The core loop: select a destination → pull for a rarity tier → receive a rarity-scaled AI adventure → complete it in real life → earn cosmetics. It runs entirely in the browser with no backend — state persists in localStorage and the Claude API is called directly from the client.

The entire app — map, gacha machine, reveal animation, profile, cosmetics, travel log — is one HTML file.

---

## How It Works

**1. Pick a destination**
An interactive Leaflet map loads over OpenStreetMap tiles, styled into a cyberpunk dark map via CSS filters. 14 pre-pinned cities have markers, but you can click anywhere on Earth — Nominatim reverse geocoding resolves the coordinates into a location name on the fly. A city search bar lets you jump directly to any place.

**2. Pull the lever**
A CSS-built gacha machine opens. Pulling the lever (or clicking the ball) rolls a rarity:

| Rarity | Chance | Color |
|---|---|---|
| Common | ~60% | Gray |
| Rare | ~30% | Neon blue |
| Ultra Rare | ~8% | Purple |
| Legendary | ~2% | Gold |

The ball animates to the rarity color, drops through the chute, and a screen flash fires. Legendary pulls trigger a confetti burst.

**3. Reveal the adventure**
A capsule ball appears. While it waits to be tapped, Claude is already generating the adventure in the background. Once ready, tapping opens the capsule and reveals the result card — rarity-scaled in depth:

- **Common** — a simple accessible activity, 2–3 sentences
- **Rare** — detailed experience with insider tips, best time, what makes it special
- **Ultra Rare** — off-the-beaten-path experience most tourists never find, vivid sensory detail, plus a mock local business deal
- **Legendary** — once-in-a-lifetime, written with cinematic wonder, full deal and code

**4. Go do it**
The result card shows the adventure title, description, best time, duration, difficulty, and any special offer. Upload a photo as proof of visit.

**5. Earn cosmetics**
Photo upload triggers a cosmetic reward scaled to the pull rarity — common pulls give common stickers and avatars, legendary pulls give legendary cosmetics. 20 cosmetics in the pool across stickers, avatars, and borders. Equipped items appear on your profile.

---

## Stack

| Layer | Tech |
|---|---|
| AI | Anthropic Claude API (`claude-sonnet-4-6`) — adventure generation |
| Maps | Leaflet.js + OpenStreetMap tiles (CartoDB Voyager) |
| Geocoding | Nominatim (reverse geo + city search) |
| State | localStorage — no backend, no accounts |
| Frontend | Vanilla JS + CSS — zero frameworks, zero dependencies beyond Leaflet |
| Deploy | Any static host — it's one HTML file |

---

## Features

### Gacha Machine
- Built entirely in CSS — gradients, animations, and a working pull lever with a knob that physically rotates
- Ball animates to rarity color on pull, drops through the chute
- Screen flash effect per rarity (blue/purple/gold)
- Confetti burst on legendary

### AI Adventure Generation
- Rarity-aware prompt: legendary pulls request "cinematic, poetic" language; common pulls get concise descriptions
- Structured JSON output with fields for title, emoji, description, bestTime, duration, difficulty
- Ultra and legendary tiers include a mock local business deal and discount code
- Location emoji mapping for known cities (Hawaii → 🌺, Paris → 🗼, Tokyo → ⛩)

### Map
- Cyberpunk dark map via CSS filter: `hue-rotate(175deg) brightness(0.55) saturate(1.2) contrast(1.4)`
- 14 pre-pinned destinations with custom pulsing neon markers
- Click anywhere on Earth → Nominatim reverse geocodes to a location name
- City search with fly-to animation
- Custom popup replaces Leaflet's default popup entirely

### Profile & Progression
- Pull stats: total pulls, rare+ count, legendary count, verified missions
- Rarity breakdown across all pulls
- Cosmetic inventory with equip/unequip toggle — equipped avatar updates in the nav
- Travel log with expandable entries showing full adventure description, metadata, and deals
- Log entries auto-expire after 7 days

### Limits
- 5 pulls per day, tracked by date in localStorage and reset at midnight
- Cosmetic pool weighted by rarity — proof upload gives cosmetics appropriate to the pull tier

---

## Design

- Dark cyberpunk aesthetic — animated rain canvas, scrolling grid background, radial neon glows, scanlines overlay
- Three fonts: Orbitron (headers/display), Rajdhani (body), DM Mono (code/metadata)
- Neon color system: pink `#ff2d8a`, blue `#00d4ff`, purple `#b44fff`, yellow `#ffe600`, green `#00ff88`
- All colors defined as CSS variables — consistent across every component
- Mobile responsive — machine scales down, nav compresses

---

## What I Learned Building This

GachaGo was my first project where the product idea came before the code. I had the concept — a gacha machine for real-world travel — and then had to figure out how to build everything that would make it feel real.

**Designing a game loop, not just a feature** — The pull → reveal → go do it → proof → reward sequence required thinking about user motivation at every step, not just what was technically possible. Getting the rarity system to influence the AI output (not just the visual) was the key insight — a legendary pull genuinely reads differently from a common one because the prompt changes.

**CSS-only mechanical UI** — The gacha machine has no canvas, no SVG, no JS for the visual — it's layered divs, gradients, border-radius, and keyframe animations. Getting the lever to rotate from the correct pivot point, the ball to drop through the chute, and the neon strips to pulse asynchronously took a lot of iteration. Learning what CSS can actually do when you push it was a highlight of this project.

**Map customization without a custom tile server** — I wanted a dark cyberpunk map but didn't want to pay for custom tiles or run a tile server. The solution was applying CSS filters directly to the Leaflet tile pane — `hue-rotate`, `brightness`, `saturate`, and `contrast` combined to turn the default CartoDB Voyager tiles into something that fits the aesthetic without touching the tile source.

**Nominatim for open geocoding** — I didn't want to pay for a geocoding API, and I didn't want the map to only work for pre-pinned cities. Nominatim (OpenStreetMap's geocoder) handles both reverse geocoding (lat/lng → location name for map clicks) and forward search (city name → coordinates) for free. Learning how to work within its rate limits and parse its address object was new.

**Structured AI output as a design constraint** — The result card has fixed fields (title, emoji, description, bestTime, duration, difficulty, deal, code). Getting Claude to reliably produce valid JSON with exactly those fields — including conditional fields for higher rarities only — required precise prompt construction and JSON parsing with error handling. Treating the prompt as an API contract rather than a freeform request changed how I thought about it.

**Single-file architecture** — Keeping everything in one HTML file was a constraint I chose deliberately, partly as a challenge and partly because it makes deployment trivial. Managing CSS scope, JS organization, and HTML structure without bundlers or modules required more discipline than a typical project.

---

## Roadmap

- [ ] Backend API proxy — move the Claude call server-side so API keys aren't client-exposed
- [ ] User accounts and persistent cloud profiles
- [ ] Claude Vision verification — actually validate photo proof using the image API
- [ ] Real local business coupon integration
- [ ] Location-locked exclusive cosmetics — some items only obtainable in specific cities
- [ ] Social profiles and leaderboards
- [ ] Pull sharing — generate a shareable card for each adventure

---

## Local Development

No build step required. Open `index.html` in a browser.

For the Claude API calls to work, you'll need an Anthropic API key entered via the in-app API KEY button. The key is stored in `localStorage` and sent directly from the browser.

Note: direct browser API calls require `anthropic-dangerous-direct-browser-access: true` in the request headers. This is fine for local development and prototyping — a production version should proxy through a backend.

---

Built by Justin Albina

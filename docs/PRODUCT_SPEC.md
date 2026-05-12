# Product Spec — Aura Off

## Core loop

```
START
  ↓
First pair appears
  ↓
[ITEM A — score visible]   [ITEM B — score hidden]
                           [HIGHER]  [LOWER]
  ↓
User taps HIGHER or LOWER
  ↓
Reveal animation (0.8s, score counts up)
  ↓
Sass line appears (1.5s)
  ↓
┌─── CORRECT? ───┐
│                 │
YES              NO
│                 │
Item B slides    GAME OVER screen
to left,         with streak,
new Item C       killer pair,
appears right    sass line,
                 share card CTA
Streak +1          ↓
│                 │
└─→ Back to start  ↓
                  Share or restart
```

### Variant: dilemma turn (every 7-10 streaks)

Instead of a regular pair:
- Two options (LEFT / RIGHT panels) with descriptive text only.
- Both scores hidden.
- User picks one.
- Reveal shows both scores. The "correct" answer is the one with higher aura.
- Sass line referencing community stat ("73% chose the same as you").
- Wrong pick = game over. Right pick = streak +1, continue.

---

## Screens

### 1. Landing (`/`)

- Hero: app logo, tagline ("How much aura do you have?"), CTA button "Start the game".
- Below the fold: 3-row carousel of example share cards (social proof). Static images, not interactive.
- Footer: about, privacy, terms.

### 2. Game (`/play`)

- Full-screen vertical layout (380px baseline).
- Top: streak counter (current + best).
- Middle: two panels stacked vertically on mobile, side-by-side on tablet+.
- Bottom: HIGHER / LOWER buttons (or LEFT / RIGHT for dilemmas), both 64px tall, full width.
- Floating top-right: daily modifier badge.

### 3. Game Over (`/g/[id]`)

- Public, indexable, shareable URL.
- Renders the share card SVG inline + a "Beat this streak" CTA.
- OG tags pre-populated for WhatsApp/IG/Twitter previews.
- Recursive: if a visitor plays from this URL and dies, their game-over page links back to the original streak that brought them.

---

## Interaction details

### Tap targets

- HIGHER/LOWER buttons: 64×full-width, min 64px tall (Apple HIG compliant).
- Item panels: tappable as fallback (tap left panel = LOWER, tap right panel = HIGHER).
- No swipe gestures. Tap-only. Simplicity > novelty.

### Animations (Framer Motion)

| Event | Animation | Duration |
|---|---|---|
| Item appears | Slide in from right, fade | 0.4s |
| Reveal correct | Number counts up, green flash | 0.8s |
| Reveal wrong | Number counts up, red flash, screen shake | 0.8s |
| Sass line | Fade in from below | 0.3s |
| Streak counter increment | Scale 1 → 1.2 → 1 | 0.2s |
| Game over | Slow zoom into killer pair, then card fades in | 1.5s |

### Sound design

Four sounds total. Web Audio API. User can mute (persists in localStorage).

| File | Trigger | License |
|---|---|---|
| `tap.mp3` | Button press | CC0 / freesound.org |
| `reveal-correct.mp3` | Correct answer reveal | CC0 |
| `reveal-wrong.mp3` | Wrong answer reveal | CC0 |
| `game-over.mp3` | Game over screen | CC0 |

Default state: muted. User opts in.

---

## State management

Pure client-side for v1. No global state library needed.

```
React state (in /play page):
  - deck: Item[]                  // pre-loaded from JSON at build
  - usedIds: Set<string>          // items consumed this game
  - currentLeft: Item              // left panel
  - currentRight: Item             // right panel (score hidden)
  - streak: number
  - phase: 'awaiting_input' | 'revealing' | 'sass_showing' | 'game_over'
  - lastSass: string
```

Persisted to localStorage:
  - `deviceId: string` (UUID, generated on first visit)
  - `bestStreak: number`
  - `totalPlays: number`
  - `soundEnabled: boolean`
  - `lastModifierSeenDate: string` (so we show the modifier modal once per day)

Synced to Supabase on game-over event only. No real-time sync, no live state.

---

## Daily modifiers

Server-time UTC. The modifier for the day is fetched once on app load and cached in memory.

- Modal popup on first launch of the day with the modifier text.
- Persistent badge top-right during gameplay reminding the modifier.
- Some modifiers affect scoring math (multiplier on certain tags). Some are purely cosmetic ("today all sass lines are in pirate speak").

See `content/modifiers.json` for the schema.

---

## Friend battle (deferred to v1.1, not v1)

Out of scope for the first launch. Mentioned here so we don't reinvent later:
- User shares a link like `/battle/{code}` after their game over.
- Friend plays the exact same deck order (deterministic seed from code).
- Side-by-side streak comparison at the end.

Implementation note: deterministic shuffle from a seeded RNG, share the seed in the URL. No backend state needed for asynchronous play.

---

## What's explicitly out of scope for v1

| Out | Why |
|---|---|
| User-generated items | Legal landmine (defamation, copyright, moderation cost) |
| Multiplayer real-time | Adds infra cost + state complexity |
| AI image analysis | Cost + privacy |
| Leaderboard global | Needs moderation, gameable, premature |
| Friend battle | v1.1 |
| Native app | Post-PMF |
| Hindi locale | Gated on kill criteria metrics |
| Monetization | Gated on 10K WAU |

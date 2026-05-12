# Architecture

## High-level

```
                  ┌──────────────────────┐
                  │   User (Mobile Web)  │
                  └──────────┬───────────┘
                             │
                  ┌──────────▼───────────┐
                  │  Vercel Edge CDN     │
                  │  (Next.js, static)   │
                  └──────────┬───────────┘
                             │
            ┌────────────────┼─────────────────┐
            │                │                 │
   ┌────────▼─────┐  ┌──────▼──────┐  ┌──────▼──────┐
   │ Static JSON  │  │  Supabase   │  │  PostHog    │
   │  (content/)  │  │  (Postgres) │  │  (events)   │
   └──────────────┘  └─────────────┘  └─────────────┘
```

- Game logic runs **entirely client-side** after first page load.
- Content (items, dilemmas, sass) is bundled as static JSON, served from Vercel CDN.
- Supabase is only touched on **game-over events and share events**. Not during gameplay.
- PostHog tracks events asynchronously, never blocks UX.

---

## Why client-side gameplay

| Reason | Why it matters |
|---|---|
| Zero latency on reveal animation | A 200ms server round-trip ruins the feel |
| Zero serverless function cost | Vercel free tier preserved |
| Works offline after first load | India connectivity is uneven |
| No DDoS risk on game endpoints | There are no game endpoints |

---

## Data flow per game session

```
1. User lands on /play
   → Client loads items.json + dilemmas.json + sass.json (static, cached)
   → deviceId.ts checks localStorage for UUID, creates if absent
   → Supabase upsert: anonymous user row (fire-and-forget)

2. Game starts
   → auraEngine.shuffle() picks first pair from deck (seeded RNG with deviceId for some determinism)
   → Render ItemPanel × 2

3. Each guess
   → auraEngine.evaluateGuess() → returns correct/incorrect + closeness
   → RevealAnimation plays
   → sass.pickLine() picks a response by outcome category
   → If correct: pick next opponent, increment streak (state only, no network)
   → If wrong: phase → 'game_over'

4. Game over
   → POST /api/games (streak, killer pair) → Supabase insert
   → Returns gameId
   → Redirect to /g/[gameId]
   → /g/[id] is server-rendered, OG tags populated for sharing

5. User shares
   → POST /api/shares (gameId, channel) → Supabase insert (fire-and-forget)
   → Web Share API or copy-to-clipboard fallback
```

---

## Folder structure (authoritative)

```
/aura-off
├── app/
│   ├── layout.tsx                  # Root layout, fonts, providers
│   ├── page.tsx                    # Landing
│   ├── play/
│   │   └── page.tsx                # Main game (client component)
│   ├── g/
│   │   └── [id]/
│   │       ├── page.tsx            # Server component, fetches game by id
│   │       └── opengraph-image.tsx # Dynamic OG image generation
│   ├── api/
│   │   ├── games/
│   │   │   └── route.ts            # POST end-of-game
│   │   ├── shares/
│   │   │   └── route.ts            # POST share event
│   │   └── stats/
│   │       └── [itemId]/
│   │           └── route.ts        # GET community pick percentages
│   └── globals.css
├── components/
│   ├── game/
│   │   ├── ItemPanel.tsx
│   │   ├── DilemmaPanel.tsx
│   │   ├── RevealAnimation.tsx
│   │   ├── StreakCounter.tsx
│   │   ├── SassLine.tsx
│   │   ├── ModifierBadge.tsx
│   │   └── ItemIllustration.tsx
│   ├── share/
│   │   ├── ShareCardSVG.tsx
│   │   └── ShareCardCanvas.tsx     # SVG → PNG converter for download
│   ├── GameOverScreen.tsx
│   └── ui/                         # Generic reusable
│       ├── Button.tsx
│       └── Modal.tsx
├── content/
│   ├── items.json
│   ├── dilemmas.json
│   ├── sass_responses.json
│   ├── modifiers.json
│   └── illustrations/
│       ├── main_character.svg
│       ├── sigma.svg
│       ├── cringe.svg
│       ├── npc.svg
│       ├── midwit.svg
│       ├── delusional.svg
│       ├── auntie_approved.svg
│       └── chronically_online.svg
├── lib/
│   ├── auraEngine.ts               # Pure functions, no React, no Supabase
│   ├── shuffle.ts                  # Seeded RNG, pair selection
│   ├── deviceId.ts                 # localStorage UUID, fingerprint fallback
│   ├── supabase.ts                 # Client setup
│   ├── sass.ts                     # Sass picker
│   ├── modifiers.ts                # Today's modifier resolver
│   └── types.ts                    # TypeScript types for Item, Dilemma, etc.
├── public/
│   ├── sounds/
│   │   ├── tap.mp3
│   │   ├── reveal-correct.mp3
│   │   ├── reveal-wrong.mp3
│   │   └── game-over.mp3
│   └── favicon.ico
├── scripts/
│   ├── validate-content.ts         # Validates JSON against schemas, runs in CI
│   └── generate-og.ts              # Pre-renders landing OG image
├── CLAUDE.md
├── README.md
└── docs/
    └── ...
```

---

## Key modules

### `lib/auraEngine.ts`

Pure functions. No side effects. Testable.

```ts
// Pick the next opponent for the current left item.
// streakLevel: 0..∞. Higher streaks = harder pairs (closer aura values).
export function pickNextOpponent(
  current: Item,
  deck: Item[],
  usedIds: Set<string>,
  streakLevel: number,
  seed?: number
): Item;

// Evaluate the user's guess.
export function evaluateGuess(
  left: Item,
  right: Item,
  guess: 'higher' | 'lower'
): EvaluationResult;

// Decide whether the next turn should be a dilemma.
export function shouldInsertDilemma(streak: number): boolean;
```

### `lib/sass.ts`

```ts
type SassOutcome = 'wrong_obvious' | 'wrong_close' | 'right_sleeper' | 'right_obvious';

export function pickSassLine(
  outcome: SassOutcome,
  recentlyUsed: Set<string>
): string;
```

Avoids repeating the same line within a session.

### `lib/deviceId.ts`

```ts
export function getOrCreateDeviceId(): string;
// Reads from localStorage. If absent, generates UUID v4.
// Fallback to fingerprint (canvas + UA hash) if localStorage blocked.
```

### `lib/supabase.ts`

```ts
import { createClient } from '@supabase/supabase-js';
export const supabase = createClient(URL, ANON_KEY, {
  auth: { persistSession: false },
});
```

No server-side client needed — all writes happen from the browser with RLS-protected anon role.

---

## RLS strategy

See `DATABASE_SCHEMA.md` for full policies. Summary:

- `users`: anyone can `insert` with a fresh device_fingerprint. Anyone can `update` their own row matched by device_fingerprint in the request header.
- `games`: insert allowed if the user_id matches the device. Select on own games. No deletes.
- `shares`: insert allowed if the game_id is owned by the user_id. No selects, no updates.
- `item_stats`: public read. Writes only via a stored procedure called after `games` insert.

---

## Why no global state library

The state of a single page (`/play`) fits comfortably in a single `useReducer`. There is no shared state across routes. Adding Zustand/Redux would be premature complexity.

If v1.1 introduces friend battles, revisit. Even then, URL state + a single `useReducer` is likely sufficient.

---

## Error handling

- Supabase writes are fire-and-forget. If they fail, the user experience continues — we lose a data point, not the game.
- Static JSON loads are critical. If `items.json` fails to load, show an error screen with retry. This should never happen (it's in the build).
- Network detection: if `navigator.onLine === false`, hide the share buttons and show "share when back online".

No global error boundaries above page level for v1. Next.js's default error.tsx is sufficient.

---

## Build-time content validation

`scripts/validate-content.ts` runs on every push via CI.

Validates:
- All `items.json` entries match the `Item` type.
- All aura scores within reasonable bounds.
- No duplicate IDs.
- All `illustration_key` values point to existing SVGs.
- All `sass_responses.json` lines under word limit, no banned words.
- Dilemmas have both options with required fields.

If validation fails, the build fails. Content is treated like code.

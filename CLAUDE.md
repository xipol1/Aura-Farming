# Aura Off — Claude Code Context

This is the authoritative context document for this project. Read first. Re-read on any architectural decision.

---

## Project in one paragraph

Aura Off is a **Higher-or-Lower style web game** where players compare two cultural moments (real icons + impossible moral dilemmas) and decide which has more "aura". The game ships with a **sarcastic narrator personality**, a shareable game-over card optimized for Instagram Reels / YouTube Shorts virality, and a curated content deck targeting **Gen Z India + global diaspora** as the primary market. The product is a content-delivery vehicle: every play is designed to terminate in a screenshot worth posting.

The core insight is that the mechanic and the content do all the work — there is **no AI at runtime, no complex state, no multiplayer in v1**. The MVP is intentionally small. The deck of cultural moments and the sass response bank are the moat.

---

## Tech stack (non-negotiable for v1)

- **Framework**: Next.js 16 (App Router) + React 19 + TypeScript strict
- **Styling**: Tailwind CSS v4 + Framer Motion for animations
- **Backend**: Supabase Free Tier (Postgres + anonymous auth)
- **Hosting**: Vercel Hobby (free)
- **CDN/DNS**: Cloudflare (free)
- **Analytics**: PostHog Cloud (free tier)
- **Sound**: Native Web Audio API + freesound.org / pixabay CC0 assets
- **Keep-alive for Supabase**: cron-job.org (free) pinging every 6 days

**Budget**: ~12€/year (domain only). Anything that adds runtime cost without measurable conversion is rejected.

---

## Architectural decisions (do not revisit without permission)

1. **No runtime AI calls.** All content is static JSON. The deck and sass bank are pre-generated and curated.
2. **No app stores.** PWA web-first. iOS/Android wrappers come post product-market fit, not before.
3. **Anonymous-only auth.** Device fingerprint + UUID → Supabase anonymous user. No email/password. No social login until users explicitly want cross-device sync.
4. **Mobile-first, vertical layout, 380px design baseline.** Desktop is responsive but not optimized.
5. **Content lives in `/content/*.json` served as static assets**, not as database rows. The Supabase database holds users, plays, shares — not items.
6. **Share card is generated client-side as SVG → Canvas → PNG.** No backend rendering.
7. **No fetching of third-party copyrighted images.** See `docs/LEGAL_RISKS.md`. Visual representation uses original SVG illustrations or text-only labels.
8. **English-only for v1.** Hindi localization happens only if PMF metrics in §KILL_CRITERIA are exceeded.

---

## Folder structure (target, not current)

```
/aura-off
├── /app                          ← Next.js App Router
│   ├── page.tsx                  ← Landing + start CTA
│   ├── /play/page.tsx            ← Main game loop
│   ├── /g/[id]/page.tsx          ← Public game-over page (OG tags, viral entry point)
│   ├── /api
│   │   ├── games/route.ts        ← POST end-of-game
│   │   ├── shares/route.ts       ← POST share event
│   │   └── stats/[itemId]/route.ts ← GET community pick percentages
│   └── layout.tsx
├── /components
│   ├── ItemPanel.tsx
│   ├── DilemmaPanel.tsx
│   ├── RevealAnimation.tsx
│   ├── StreakCounter.tsx
│   ├── SassLine.tsx
│   ├── ShareCardSVG.tsx          ← CRITICAL component
│   ├── GameOverScreen.tsx
│   └── ItemIllustration.tsx      ← Renders SVG illustration per tag
├── /content
│   ├── items.json
│   ├── dilemmas.json
│   ├── sass_responses.json
│   ├── modifiers.json
│   └── illustrations/            ← SVG files per tag
├── /lib
│   ├── auraEngine.ts             ← Core scoring + pair selection logic
│   ├── shuffle.ts                ← Pair selection algorithm
│   ├── deviceId.ts               ← Anonymous device fingerprint
│   ├── supabase.ts               ← Client setup
│   └── sass.ts                   ← Random response picker
├── /public
│   ├── /sounds                   ← tap, reveal-correct, reveal-wrong, game-over
│   └── /og                       ← OG image templates
├── CLAUDE.md                     ← This file
├── README.md
└── package.json
```

---

## Code conventions

- **TypeScript strict mode**. No `any`. No `@ts-ignore` without comment explaining why.
- **Components**: PascalCase, one component per file, default exports for pages, named exports for components.
- **Hooks**: prefix `use`, return objects not tuples when >2 values.
- **Tailwind**: use `cn()` helper for conditional classes. No inline styles unless dynamic from JSON (color tokens by tag).
- **No CSS modules, no styled-components, no emotion.** Tailwind only.
- **Server components by default**. Use `'use client'` only when needed (animations, event handlers, localStorage).
- **No `useEffect` for data fetching in the game loop**. Pre-load `items.json` at build time (static import). The game is fully client-side once loaded.
- **All user-facing strings live in JSON content files, not in components.** This enables localization without code changes.
- **Sass lines, item labels, sound paths, color tokens**: all data, not code.

---

## Content system

The game is a content engine. Three JSON files drive everything:

### `/content/items.json`
Array of objects. Each represents one cultural moment.

```ts
type Item = {
  id: string;                    // 'i_001'
  label: string;                 // 'Messi raising the World Cup'
  aura: number;                  // -200000 to +400000
  tag: ItemTag;                  // determines color palette
  category: ItemCategory;        // for filtering / themed decks
  cultural_anchor: 'global' | 'india' | 'diaspora';
  illustration_key?: string;     // points to SVG in /content/illustrations/
};

type ItemTag = 'main_character' | 'sigma' | 'cringe' | 'npc' 
             | 'midwit' | 'delusional' | 'auntie_approved' | 'chronically_online';

type ItemCategory = 'cricket_india' | 'football_global' | 'cinema_iconic'
                  | 'anime_sigma' | 'tech_founder' | 'politics_soft'
                  | 'viral_moments' | 'bollywood' | 'music_culture'
                  | 'anti_aura' | 'sigma_everyday' | 'cringe_legendary';
```

### `/content/dilemmas.json`
Array of impossible-choice items. Different shape from regular items.

```ts
type Dilemma = {
  id: string;                    // 'd_001'
  prompt: string;                // 'Choose:'
  option_a: { label: string; aura_if_chosen: number; tag: ItemTag };
  option_b: { label: string; aura_if_chosen: number; tag: ItemTag };
  category: DilemmaCategory;
  cultural_anchor: 'global' | 'india' | 'diaspora';
};

type DilemmaCategory = 'trolley' | 'friends_vs_partner' | 'life_choices'
                     | 'india_cultural' | 'cringe_vs_cringe' | 'sigma_extreme';
```

### `/content/sass_responses.json`
Sass response bank, categorized by outcome.

```ts
type SassBank = {
  wrong_obvious: string[];       // user failed an easy one (player aura penalty)
  wrong_close: string[];         // user failed but the call was close
  right_sleeper: string[];       // user picked a non-obvious correct (player aura bonus)
  right_obvious: string[];       // user picked the easy correct
  dilemma_neutral: string[];     // for dilemmas — neutral commentary with stat placeholder
  game_over: string[];           // shown on the share card after death
};
```

**Rules for sass content**:
- Each line ≤ 12 words
- No attacks on identity (gender, race, sexuality, religion)
- No self-harm references
- 30% of lines should be in spanglish/hinglish code-switching tone
- Game-over lines must work on the share card without context

### `/content/modifiers.json`
Daily global modifiers. The server returns the modifier for the current UTC date.

```ts
type Modifier = {
  id: string;                              // 'm_NNN'
  text: string;                            // 'Today: ...' (max 60 chars)
  affects_tag: ItemTag | null;             // null when no tag effect
  affects_category: ItemCategory | null;   // null when no category effect
  multiplier: number;                      // 0.5 to 2.0, -1.0 for inversion, 1.0 if cosmetic-only
  cosmetic_only?: boolean;                 // defaults false
  cosmetic_effect?: string;                // only present when cosmetic_only is true
};

// The modifier for the current UTC date is picked by rotation:
//   rotation[dayOfYearUTC % rotation.length]
// v1.1 may add date-pinned event modifiers (Diwali, IPL final, etc).
```

---

## Core gameplay logic (`/lib/auraEngine.ts`)

The engine is pure functional. Given a current item + deck + difficulty step, returns next pair.

```ts
function pickNextOpponent(current: Item, deck: Item[], streakLevel: number): Item;
function evaluateGuess(left: Item, right: Item, guess: 'higher' | 'lower'): {
  correct: boolean;
  delta: number;                 // absolute aura difference
  closeness: 'easy' | 'close' | 'trivial';
};
function pickSassLine(outcome: Outcome, bank: SassBank): string;
function shouldInsertDilemma(streak: number): boolean;  // every 7-10 streaks
```

**Pair selection rules**:
1. First pair: random from deck.
2. Subsequent pairs: opponent's aura is within 30-60% delta of current (not trivial, not impossible).
3. Every 10 streaks: force an outlier (an "absurd" item — score >100k or <-50k).
4. Every 7-10 streaks: insert a dilemma instead of a regular pair.
5. Never repeat an item in the same game session.

---

## Database schema (Supabase)

Minimal. See `docs/DATABASE_SCHEMA.md` for full DDL.

```sql
-- Anonymous users
create table users (
  id uuid primary key default gen_random_uuid(),
  device_fingerprint text unique not null,
  created_at timestamptz default now(),
  best_streak int default 0,
  total_plays int default 0,
  total_shares int default 0
);

-- Game sessions
create table games (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references users(id) on delete cascade,
  streak int not null,
  killed_by_item_id text not null,
  killed_by_opponent_id text not null,
  created_at timestamptz default now()
);

-- Share events
create table shares (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references users(id) on delete cascade,
  game_id uuid references games(id) on delete cascade,
  channel text check (channel in ('whatsapp','instagram','twitter','copy_link','other')),
  created_at timestamptz default now()
);

-- Per-item community stats (aggregated, denormalized for speed)
create table item_stats (
  item_id text primary key,
  times_compared int default 0,
  times_picked_higher int default 0
);
```

RLS: anonymous users can insert their own games/shares, can read item_stats. Cannot read other users' data.

---

## Commands

```bash
# Dev
npm run dev                # local dev server
npm run build              # production build
npm run start              # serve production build locally
npm run lint               # ESLint
npm run typecheck          # tsc --noEmit

# Content
npm run validate-content   # validates all JSON against TypeScript schemas
npm run generate-og        # pre-generates OG images for landing/play pages

# Deploy
git push origin main       # auto-deploys to Vercel
```

---

## Build/run order for Claude Code

When Claude Code picks up this project, execute in this order:

1. Read `CLAUDE.md` (this file) → understand context
2. Read `roadmap/ROADMAP.md` → understand current phase
3. Read `roadmap/TASKS.md` → pick the next unchecked task
4. Read relevant `docs/*.md` for the task domain
5. Read relevant `content/*.json` if the task touches content
6. Execute. Update task status in `TASKS.md`. Commit.

**Never** start building features not in `TASKS.md` without updating it first.

---

## What this project is NOT

- Not a social network.
- Not a multiplayer game.
- Not an AI-powered chatbot or analyzer.
- Not a Reddit-style submission engine where users submit content (legal landmine — see `docs/LEGAL_RISKS.md`).
- Not a freemium SaaS. Monetization is post-validation only.
- Not a native mobile app in v1.

If a feature request feels like it's expanding scope into any of the above, push back. The MVP must remain ruthlessly small.

---

## Known unknowns / open decisions

1. Final domain name: `auraoff.app`? `auraoff.com`? `getaura.app`? — pending availability check.
2. Final logo / wordmark.
3. Sound design vibe: muted/clinical vs. dramatic/maximalist — to be decided after seeing first share card prototype.
4. Whether to add a Hindi version in week 4 or wait for kill criteria evaluation at week 6.

These are flagged here so Claude Code knows to surface them when relevant, not to assume defaults.

# Tasks

This is the operational checklist Claude Code follows. Tasks are ordered. Complete in sequence unless explicitly parallelizable.

**Format**: `- [ ]` = open, `- [x]` = done. Add a commit short hash after completion: `- [x] Task description (a1b2c3d)`.

---

## Week 1 — Foundation + Content

### Day 1-2: Project bootstrap

- [ ] Create new Next.js 15 project with App Router, TypeScript strict, Tailwind v4: `npx create-next-app@latest aura-off-app --typescript --tailwind --app --no-src-dir`
- [ ] Initialize git, create remote on GitHub (private), push initial commit
- [ ] Link to Vercel project, confirm preview deploys work on PR
- [ ] Add `prettier`, `eslint` config matching the conventions in `CLAUDE.md`
- [ ] Set up `husky` + `lint-staged` for pre-commit
- [ ] Copy this `aura-off/` documentation folder into the repo root
- [ ] Move `CLAUDE.md` to the repo root (already done if you copied the folder)
- [ ] Create `.env.example` with the variables listed in `docs/TECH_STACK.md`

### Day 2-3: Supabase setup

- [ ] Create new Supabase project (EU region)
- [ ] Run the full DDL from `docs/DATABASE_SCHEMA.md` in the SQL editor
- [ ] Verify RLS policies are enabled on all tables
- [ ] Add `SUPABASE_URL` and `SUPABASE_ANON_KEY` to Vercel env vars (Production + Preview)
- [ ] Create `lib/supabase.ts` with the client setup
- [ ] Test from local dev: anonymous user insert + select

### Day 3-4: Types and content validation

- [ ] Create `lib/types.ts` with `Item`, `Dilemma`, `SassBank`, `Modifier`, `ItemTag`, `ItemCategory`, `DilemmaCategory` types matching `CLAUDE.md` and `CONTENT_GUIDELINES.md`
- [ ] Copy `content/*.json` files into the repo
- [ ] Create `scripts/validate-content.ts` that:
  - Loads each JSON
  - Validates against TypeScript types using `zod` (allowed dependency)
  - Checks aura bounds, label length, sass word count, no duplicate IDs, no banned words
  - Exits with non-zero code on failure
- [ ] Add `npm run validate-content` to `package.json`
- [ ] Wire `validate-content` into the pre-push git hook

### Day 4-5: Core engine

- [ ] Create `lib/auraEngine.ts` with:
  - `pickNextOpponent(current, deck, usedIds, streakLevel, seed?): Item`
  - `evaluateGuess(left, right, guess): EvaluationResult`
  - `shouldInsertDilemma(streak: number): boolean`
- [ ] Create `lib/shuffle.ts` with a seeded RNG (`xoroshiro128+` or similar pure JS)
- [ ] Create `lib/sass.ts` with `pickSassLine(outcome, recentlyUsed): string`
- [ ] Create `lib/deviceId.ts` with `getOrCreateDeviceId(): string`
- [ ] Create `lib/modifiers.ts` with `getTodayModifier(): Modifier` (deterministic from UTC date)
- [ ] Write minimal unit tests (Vitest) for `auraEngine.ts` and `shuffle.ts` — these must be deterministic

### Day 5-6: Placeholder game UI

- [ ] Create `app/page.tsx` (landing) with title + "Start the game" button → `/play`
- [ ] Create `app/play/page.tsx` (client component) with:
  - `useReducer` for game state
  - Render two `ItemPanel` components (placeholder styling)
  - HIGHER / LOWER buttons
  - On click: call `evaluateGuess`, advance or end game
  - On game over: render placeholder game-over screen
- [ ] Create `components/game/ItemPanel.tsx` with label, score (hidden if `revealed=false`)
- [ ] Create `components/game/DilemmaPanel.tsx` for dilemma turns
- [ ] Test in dev: full game loop playable without styling

### Day 6-7: Backend integration

- [ ] Implement `app/api/games/route.ts` POST endpoint inserting game-over to Supabase
- [ ] Implement `app/api/shares/route.ts` POST endpoint
- [ ] On game start: client calls Supabase to upsert user by device fingerprint
- [ ] On game over: client POSTs to `/api/games`, gets back `gameId`, redirects to `/g/[gameId]`
- [ ] Create stub `app/g/[id]/page.tsx` showing just the streak number (no design yet)
- [ ] Verify end-to-end: load `/play`, play to death, redirect to `/g/[id]` with valid id

**End of Week 1**: A functional but ugly game. Plays through. Stores data. Has a public game-over URL.

---

## Week 2 — Game Loop + Share Card

### Day 8: Design system foundations

- [ ] Configure Tailwind with the color tokens, spacing, and font families from `docs/DESIGN_SYSTEM.md`
- [ ] Set up Google Fonts loading (Space Grotesk, Inter, JetBrains Mono) via Next.js font optimization
- [ ] Create `components/ui/Button.tsx` with primary and ghost variants
- [ ] Create a Storybook page or `/dev/design` route to view all design tokens

### Day 8-9: SVG illustrations

- [ ] Generate 8 monoline SVG illustrations matching the style in `docs/DESIGN_SYSTEM.md`:
  - `main_character.svg`
  - `sigma.svg`
  - `cringe.svg`
  - `npc.svg`
  - `midwit.svg`
  - `delusional.svg`
  - `auntie_approved.svg`
  - `chronically_online.svg`
- [ ] Save in `content/illustrations/`
- [ ] Create `components/game/ItemIllustration.tsx` that renders the right SVG by tag

### Day 9-10: Apply design to game screens

- [ ] Restyle `ItemPanel.tsx` to match the spec in `docs/DESIGN_SYSTEM.md`
- [ ] Restyle `DilemmaPanel.tsx`
- [ ] Restyle `StreakCounter.tsx` with flame emoji + Space Grotesk numbers
- [ ] Restyle `ModifierBadge.tsx` (pill, top-right)
- [ ] Restyle landing and game-over screens

### Day 10-11: Animations

- [ ] Install `framer-motion`
- [ ] Animate item entry (slide-in from right, 400ms)
- [ ] Animate reveal: number count-up over 800ms, color flash on screen edge
- [ ] Animate streak counter increment (scale 1 → 1.2 → 1)
- [ ] Animate sass line entry (fade + slide up, 300ms)
- [ ] Animate game-over screen entry (slow zoom into killer pair, 1.5s)
- [ ] Respect `prefers-reduced-motion`: all animations become fade-only

### Day 11-12: Share card

- [ ] Create `components/share/ShareCardSVG.tsx` matching the spec in `docs/SHARE_CARD_SPEC.md`
- [ ] Install `html-to-image` (12 KB allowed dep)
- [ ] Create `components/share/ShareCardCanvas.tsx` that rasterizes the SVG to a PNG blob
- [ ] Implement `handleShare(gameId)` flow with Web Share API + clipboard fallback
- [ ] Test on real Chrome Android — most likely place for bugs

### Day 12: Public game-over page

- [ ] Build full `app/g/[id]/page.tsx`: server component that fetches game by id from Supabase, renders the share card inline, adds a "Beat my streak" CTA linking to `/play`
- [ ] Add `app/g/[id]/opengraph-image.tsx` that generates a 1200×630 OG image dynamically per game
- [ ] Verify OG previews look right in WhatsApp, Twitter, Instagram (paste a link in each)

### Day 13: Sound

- [ ] Download 4 CC0 sound effects (tap, reveal-correct, reveal-wrong, game-over) from freesound.org or pixabay
- [ ] Place in `public/sounds/`
- [ ] Create `lib/sound.ts` with a sound manager that respects user mute setting
- [ ] Add mute toggle button in the game UI, persist to localStorage
- [ ] Default state: muted (user opts in)

### Day 14: Analytics + telemetry

- [ ] Set up PostHog with cookieless mode
- [ ] Add events: `game_start`, `guess_made` (with correct/incorrect), `dilemma_shown`, `game_over` (with streak), `share_clicked`, `share_completed` (with channel)
- [ ] Verify events fire in production
- [ ] Set up PostHog cohort for D1, D7 retention tracking

**End of Week 2**: A polished, animated, share-card-generating game ready for internal beta testing.

---

## Week 3 — Polish + Launch

### Day 15-16: Legal compliance

- [ ] Trademark search: EUIPO + USPTO + India IP Office for "Aura Off"
- [ ] If conflict: pick alternative name from shortlist, update everything
- [ ] Write privacy policy (use a template, customize for our data flows) → publish at `/privacy`
- [ ] Write terms of service → publish at `/terms`
- [ ] Implement age gate: modal on first visit, asks DOB, stores in localStorage, blocks under 18
- [ ] Implement `/api/data/delete` endpoint that deletes user + games + shares by device fingerprint
- [ ] Add footer with links to privacy, terms, delete-my-data

### Day 16-17: Domain + infra

- [ ] Register chosen domain (Namecheap, ~12€)
- [ ] Configure DNS via Cloudflare (set Vercel as origin, proxy through Cloudflare)
- [ ] Add custom domain to Vercel project, verify SSL
- [ ] Set up cron-job.org pinging `/api/keep-alive` every 24 hours
- [ ] Add Sentry (optional, free tier) for error tracking

### Day 17-18: Performance + accessibility

- [ ] Run Lighthouse on production URL. Target: Performance > 90, A11y > 90, Best Practices > 95
- [ ] Fix any LCP issues (preload critical fonts, lazy-load non-critical images)
- [ ] Add proper ARIA labels to buttons and panels
- [ ] Test full game with screen reader (VoiceOver on iOS, TalkBack on Android)
- [ ] Verify all interactive elements meet 44×44px tap target

### Day 18-19: Real-device testing

- [ ] Test on real Chrome Android (multiple devices if possible)
- [ ] Test on real Safari iOS
- [ ] Test on Samsung Internet
- [ ] Test on slow 4G (Chrome DevTools throttling at minimum)
- [ ] Fix any visual or interaction bugs found

### Day 19-20: Reels production

- [ ] Record 30 short Reels using the live build
- [ ] Use the 5 formats in `distribution/REELS_SCRIPT_TEMPLATES.md` (6 each)
- [ ] Edit on phone or with CapCut, add captions
- [ ] Schedule first batch of 9 for launch day (3/day at 9am, 5pm, 9pm IST)

### Day 20-21: Launch

- [ ] Set Instagram, YouTube, Twitter, Reddit accounts to active
- [ ] Pin first Reel on Instagram
- [ ] Cross-post launch announcement to:
  - Personal Twitter / X
  - Personal LinkedIn (low-key)
  - Indie hackers (only if mood is right, not critical)
  - Reddit: r/IndianTeenagers, r/IndianDankMemes (native posts, not link spam)
- [ ] Monitor first 4 hours actively: respond to every comment
- [ ] Take notes of what's working and what's broken
- [ ] Don't make changes to the product on launch day — let it breathe

**End of Week 3**: Public launch. Move to `docs/DISTRIBUTION_PLAN.md` for the 90-day playbook.

---

## Backlog (not for v1)

Items deferred to v1.1 or beyond. Do not start without explicit founder approval.

- [ ] Hindi localization
- [ ] Friend battle mode (deterministic-seed share link)
- [ ] Themed deck purchases
- [ ] Aura Off Pro premium tier
- [ ] Sponsored items
- [ ] Leaderboards
- [ ] Native mobile wrapper (Capacitor)
- [ ] Email opt-in for "weekly aura report"
- [ ] A/B testing framework
- [ ] Real-time multiplayer

---

## Notes for Claude Code

- Commit message style: `feat(domain): short summary` or `fix(domain): short summary`. Domain = `engine`, `ui`, `share`, `content`, `infra`, `legal`.
- After each completed task, update this file with `[x]` and the commit short hash.
- If you're blocked on a task that requires an external decision (e.g. trademark conflict found), surface it in the chat with the founder. Don't make naming decisions unilaterally.
- Stick to the order. The order is meaningful: it minimizes rework and matches dependency chains.

# Roadmap

## Mission

Ship Aura Off from zero to public launch in 3 weeks. Day 1 of Week 1 starts the day Claude Code is opened in this repo.

The roadmap is split into three weeks. Each week has clear deliverables. If a week slips, the next week absorbs the slip — total budget is hard-capped at 4 weeks.

---

## Week 1 — Foundation + content

**Goal**: A fully running game in dev that uses the real content, plays through, dies, and shows a placeholder game-over screen.

**Deliverables**:
- Next.js 15 project bootstrapped, deployed to Vercel preview
- Supabase project created, schema applied (`DATABASE_SCHEMA.md`)
- TypeScript types defined for Item, Dilemma, Modifier, SassBank
- Content JSON files validated against types via `validate-content.ts`
- Core game loop functional with placeholder UI (no styling)
- Anonymous device ID + Supabase user creation on first visit
- Game-over insert to `games` table working

**Out of scope this week**: pixel-perfect design, share card rendering, sound, animations.

See `WEEK_1_TASKS.md` for the granular checklist.

---

## Week 2 — Game loop + share card

**Goal**: A polished, animated, share-card-generating game that's ready for internal beta.

**Deliverables**:
- Final design applied per `DESIGN_SYSTEM.md`
- All animations from `PRODUCT_SPEC.md` implemented with Framer Motion
- 8 SVG illustrations created (one per tag)
- `ShareCardSVG.tsx` rendering and rasterizing to PNG via canvas
- Native share + copy-link fallback both working
- Public `/g/[id]` page with OG image generation
- Daily modifier system rendering correctly
- Sound system with user mute toggle, persisted to localStorage
- PostHog events for: game_start, guess_made, game_over, share_clicked, share_completed
- Mobile + desktop responsive

**Out of scope this week**: backend optimizations, themed decks, leaderboards.

See `WEEK_2_TASKS.md` for the granular checklist.

---

## Week 3 — Polish + launch

**Goal**: Public launch with 30 pre-recorded Reels ready to post.

**Deliverables**:
- Privacy policy at `/privacy`
- Terms of service at `/terms`
- Age gate modal on first visit (16+ check)
- Delete-my-data endpoint at `/api/data/delete`
- Trademark search complete for "Aura Off" (or alternate name selected)
- Domain registered + DNS configured via Cloudflare
- 30 Reels recorded using the dev build
- Instagram, YouTube, Twitter, Reddit accounts created and locked
- cron-job.org configured for Supabase keep-alive
- Lighthouse scores: Performance > 90, Accessibility > 90, Best Practices > 95
- Tested on real Chrome Android + Safari iOS devices
- 5 internal beta users played 10+ games and gave qualitative feedback
- Launch day plan documented (post times, channels, follow-up)

**Out of scope this week**: anything not on the deliverables list. Resist scope creep with extreme prejudice.

See `WEEK_3_TASKS.md` for the granular checklist.

---

## Post-launch

Weeks 4-12 are governed by `docs/DISTRIBUTION_PLAN.md` and `docs/KILL_CRITERIA.md`. The roadmap above ends at launch. After launch, the next document Claude Code reads is `DISTRIBUTION_PLAN.md`.

---

## How Claude Code uses this roadmap

1. Open `roadmap/TASKS.md` and find the current unchecked task.
2. Read the relevant docs for that task domain.
3. Execute the task.
4. Update `TASKS.md` with `[x]` and a commit hash.
5. Commit and push.
6. Move to next task.

Do not work on tasks outside the current week without updating the roadmap first.

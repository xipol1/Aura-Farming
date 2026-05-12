# Tech Stack

## Production stack (verified zero-cost at MVP scale, May 2026)

| Component | Service | Tier | Free limits | Notes |
|---|---|---|---|---|
| Hosting + edge | Vercel Hobby | Free | 100 GB bandwidth/mo, 100k function invocations/day | Sufficient for ~50k MAU |
| Database + Auth | Supabase Free | Free | 500 MB DB, 1 GB storage, 50k MAU | Project pauses after 7 days inactivity → mitigate with cron ping |
| Framework | Next.js 15 (App Router) + React 19 | OSS | — | Server components, streaming |
| Language | TypeScript 5.x strict | OSS | — | No `any` |
| Styling | Tailwind CSS v4 | OSS | — | Utility-first, no extra runtime |
| Animation | Framer Motion 11+ | OSS | — | For reveal + transitions |
| Analytics | PostHog Cloud Free | Free | 1M events/mo | Self-host fallback if cap hit |
| Errors | Sentry Free | Free | 5k events/mo | Optional v1.1 |
| DNS + CDN | Cloudflare | Free | Unlimited | Free SSL, DDoS protection |
| Keep-alive | cron-job.org | Free | — | Ping Supabase every 6 days |
| Domain | Namecheap `.app` TLD | — | — | ~12€/year — **only real cost** |

**Total year 1 cost**: ~12€.

When the project crosses Supabase Free limits (50k MAU or 500 MB DB), upgrade to Supabase Pro: 25€/month. By that point, monetization should already be active. See `MONETIZATION.md`.

---

## Stack decisions and rejected alternatives

| Choice | Alternative considered | Why rejected |
|---|---|---|
| Next.js App Router | Astro / SvelteKit | Vercel native integration, SSR for OG tags on share pages, React talent abundance |
| Supabase | Firebase / PlanetScale / Neon | Postgres + auth + free anonymous in one. Open source. RLS gives multi-tenant isolation for free. |
| Tailwind v4 | CSS Modules / Vanilla Extract | Speed of iteration with Claude Code. No runtime cost. |
| Framer Motion | GSAP / React Spring | Friendlier API for declarative React animations. Free. |
| Anonymous auth | Email/password / Google OAuth | Friction. The game must work on first tap. Sync via OAuth becomes optional in v1.1. |
| Static JSON for content | DB-stored content | Content is the asset. Curated by humans. Versioned in git. No reason to put it in DB. |
| Vercel | Cloudflare Pages / Netlify | Best-in-class for Next.js. Free tier is generous enough. |
| Web-first PWA | Native iOS/Android | $99 Apple Dev + $25 Google Play = unnecessary friction. Distribution is Reels/Shorts, not app stores. |

---

## Performance budget

Hard ceilings. Anything above triggers refactor.

| Metric | Target | Hard ceiling |
|---|---|---|
| LCP (mobile 4G India) | < 1.5s | 2.5s |
| TTI | < 2s | 3s |
| Bundle size (initial JS) | < 80 KB gzipped | 150 KB |
| Items JSON size | < 200 KB | 400 KB |
| Share card render time | < 200ms | 500ms |
| Time to first interaction | < 1s after page load | 2s |

Tools: Lighthouse CI on every PR, WebPageTest manual checks pre-launch.

---

## Environment variables

```bash
# .env.local
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxx...
NEXT_PUBLIC_POSTHOG_KEY=phc_xxx
NEXT_PUBLIC_POSTHOG_HOST=https://eu.i.posthog.com
NEXT_PUBLIC_APP_URL=https://auraoff.app

# Only used in build/CI, never client-side
SUPABASE_SERVICE_ROLE_KEY=eyJxxx...      # for migrations only
```

Never put service keys in `NEXT_PUBLIC_*` vars.

---

## Browser support matrix

| Browser | Version | Priority |
|---|---|---|
| Chrome Android | Latest 2 | P0 — primary India target |
| Safari iOS | Latest 2 | P0 |
| Samsung Internet | Latest 2 | P1 — common in India |
| MIUI Browser | Latest 2 | P1 — Xiaomi devices in India |
| Chrome Desktop | Latest 2 | P2 |
| Firefox / Edge | Latest 2 | P3 — best effort |

Test on real device for at least Chrome Android + Safari iOS before any launch.

---

## CI/CD

- GitHub → Vercel auto-deploy on push to `main`.
- Preview deploys on every PR.
- Pre-commit: prettier + eslint via husky.
- Pre-push: typecheck + content validation script.
- No tests required for v1 (move fast). v1.1 adds Playwright for critical paths.

---

## What we won't add until forced to

- Redis / KV store
- Queue system (BullMQ, Inngest)
- Background workers
- WebSockets
- Real-time subscriptions
- ORM (Prisma) — Supabase JS client is sufficient
- State management library (Zustand, Redux)
- Form library (the only "form" is the game itself)
- i18n library (single-language v1)
- A/B testing framework
- Feature flags

Add only when a concrete metric or pain point demands it.

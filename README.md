# Aura Off

> Higher-or-Lower for the aura economy. Battle cultural moments and impossible dilemmas. Die laughing. Share the card.

---

## What this is

A web game where two cultural moments (or moral dilemmas) appear side by side. You guess which has more aura. Streak goes up. You die. You share the card. Cycle repeats.

**Target market**: Gen Z India Tier 1 + global diaspora.
**Distribution**: Instagram Reels, YouTube Shorts, WhatsApp, Reddit. No TikTok (banned in India).
**Budget**: ~12€/year. Built with Claude Code.

---

## Navigating this repo

| File | Purpose |
|---|---|
| [`CLAUDE.md`](./CLAUDE.md) | Authoritative context for Claude Code. **Read first.** |
| [`docs/PRODUCT_SPEC.md`](./docs/PRODUCT_SPEC.md) | Game mechanics, loop, UI behavior |
| [`docs/TECH_STACK.md`](./docs/TECH_STACK.md) | Stack details + free-tier limits |
| [`docs/ARCHITECTURE.md`](./docs/ARCHITECTURE.md) | Code structure, decisions, patterns |
| [`docs/DATABASE_SCHEMA.md`](./docs/DATABASE_SCHEMA.md) | Supabase DDL + RLS policies |
| [`docs/SHARE_CARD_SPEC.md`](./docs/SHARE_CARD_SPEC.md) | Spec for the viral share card |
| [`docs/DESIGN_SYSTEM.md`](./docs/DESIGN_SYSTEM.md) | Colors, typography, tag palettes |
| [`docs/LEGAL_RISKS.md`](./docs/LEGAL_RISKS.md) | Copyright, DPDPA, GDPR — read before adding images |
| [`docs/DISTRIBUTION_PLAN.md`](./docs/DISTRIBUTION_PLAN.md) | 90-day post-launch playbook |
| [`docs/MONETIZATION.md`](./docs/MONETIZATION.md) | Phase 2 revenue paths (post-PMF) |
| [`docs/KILL_CRITERIA.md`](./docs/KILL_CRITERIA.md) | When to kill, when to pivot |
| [`content/items.json`](./content/items.json) | 100+ cultural moments with aura scores |
| [`content/dilemmas.json`](./content/dilemmas.json) | 50+ impossible-choice dilemmas |
| [`content/sass_responses.json`](./content/sass_responses.json) | 200+ sarcastic narrator lines |
| [`content/modifiers.json`](./content/modifiers.json) | Daily global aura modifiers |
| [`content/CONTENT_GUIDELINES.md`](./content/CONTENT_GUIDELINES.md) | How to add/edit content |
| [`roadmap/ROADMAP.md`](./roadmap/ROADMAP.md) | 3-week build plan |
| [`roadmap/TASKS.md`](./roadmap/TASKS.md) | Granular checklist for Claude Code |
| [`distribution/REELS_SCRIPT_TEMPLATES.md`](./distribution/REELS_SCRIPT_TEMPLATES.md) | Reels formats that work |
| [`distribution/CREATOR_OUTREACH_TEMPLATE.md`](./distribution/CREATOR_OUTREACH_TEMPLATE.md) | Cold DM scripts |

---

## Starting development

Prerequisite: Node 20+, a Supabase project, a Vercel project linked to this repo.

```bash
git clone <repo>
cd aura-off
npm install
cp .env.example .env.local      # fill SUPABASE_URL, SUPABASE_ANON_KEY
npm run dev
```

Open Claude Code in the repo root. It will read `CLAUDE.md` and `roadmap/TASKS.md` automatically.

---

## Status

- [ ] Week 1 — Foundation + content
- [ ] Week 2 — Game loop + share card
- [ ] Week 3 — Polish + launch

See [`roadmap/ROADMAP.md`](./roadmap/ROADMAP.md) for the current week and pending tasks.

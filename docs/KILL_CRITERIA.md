# Kill Criteria

The most important document in this folder. **Read this when you're emotionally attached and the data is bad.**

---

## Why this exists

Founders kill projects too late. Sunk-cost fallacy + ego + identity-attachment delays the decision. This document is the dispassionate version of the founder, written before things got hard.

The rule: **if criteria below are not met, the project dies on the stated date. No extensions. No "let's give it one more week."**

---

## Hard kill at Week 6 post-launch

Measured at exactly **42 days after public launch**. Take the rolling 7-day average ending on day 42.

The project ships to the bin (archived, not maintained) if **any two** of these are below threshold:

| Metric | Threshold |
|---|---|
| Total share cards generated (cumulative, organic) | < 1,000 |
| Share rate (shares / completed games) | < 5% |
| D7 retention | < 5% |
| Highest-performing Reel views | < 10,000 |
| Total registered devices | < 5,000 |

"Organic" means not driven by paid promotion. Since we have no paid promotion, all of it is organic. The number stands.

---

## Soft pivot triggers at Week 6

If exactly **one** metric fails but the rest pass, this is not a kill — it's a pivot signal. The diagnostic:

| Failing metric | Likely cause | Pivot direction |
|---|---|---|
| Share rate < 5%, retention healthy | Card not viral enough | Redesign share card, test 3 variants over 2 weeks |
| Retention < 5%, share rate healthy | Game too thin / repetitive | Add Brainrot Rush mode or themed decks |
| Reels views low, in-app metrics healthy | Distribution issue | Pivot content format, test 3 new Reel templates |
| Registered devices low, conversion healthy | Acquisition issue | Increase cold DM volume, find new subreddits |
| Card count low, all other healthy | Cards never reach completion | Reduce game length, ensure earlier wins create cards |

A pivot earns 2 additional weeks. After that, kill criteria re-evaluated.

---

## Hard milestones for continuation past Week 6

To continue building past Week 6 with conviction, we want:

| Metric | Week 6 target |
|---|---|
| WAU | 5,000+ |
| Share cards generated total | 5,000+ |
| Share rate | 15%+ |
| D7 retention | 15%+ |
| K-factor (new users per share) | 0.5+ |
| At least one Reel | > 100k views |
| At least three creators have organically posted | — |

Hitting these = clear go signal for Phase 2 (Hindi locale, monetization, themed decks).

---

## Time budget kill criteria

Even if metrics are passing, the project dies if it consumes too much founder time.

**Hard ceiling**: 15 hours per week sustained for the founder.

This is non-negotiable because of competing priorities:
- PSTD Los Alcázares contract obligations (primary income)
- Adflow (primary venture)
- Kalmas legal matters

If Aura Off requires >15 h/week for more than 2 consecutive weeks, choose one:
1. Reduce scope (kill features in the backlog).
2. Find a co-builder (revenue share, no equity until revenue exists).
3. Kill the project.

The project is good if it stays a side-project hit. It is a problem if it becomes the main thing.

---

## Cost kill criterion

If monthly infrastructure cost exceeds **30€** at any point during v1 (pre-monetization), pause and audit. The free-tier strategy is foundational. If we're breaking it, something is wrong with traffic patterns or our build.

After monetization activation, infra cost can scale up to **25% of monthly revenue**, max. Above that, optimize before adding features.

---

## Legal kill criterion

A credible legal threat to the brand (trademark conflict that can't be resolved without paying, repeated takedown notices, regulatory inquiry) is an immediate kill OR rebrand decision within 7 days.

We do not litigate. We do not invest in legal defense pre-revenue. The product is satire that we accept could face challenges — we just don't fight them.

---

## Founder mental health criterion

If the project is causing measurable harm to the founder's wellbeing, partner relationship, sleep, or capacity to deliver on contracted obligations: kill or pause immediately. No metrics override this.

---

## What "kill" means in practice

When we kill the project:

1. Stop posting on social channels. Pin a final post if it feels right; otherwise just stop.
2. Set Supabase project to paused (don't delete — keep data accessible for 90 days in case of resurrection insights).
3. Set Vercel project to "Inactive". Domain stays live with a static "Thanks for playing" page.
4. Export final analytics + content library to a local archive (for future post-mortem).
5. Write a public post-mortem on what worked, what didn't, what we'd do differently. Publish it. This has long-term reputational value.
6. Move on.

**Do not**: delete the repo, the domain, the social handles. Future use unknown.

---

## What this document forbids

- "But the trend is just starting to pick up, give it more time."
- "We need to add feature X to know if it works."
- "Just one more Reel."
- "If we localize to Hindi it could change everything."
- "My friend says it's actually really cool."

These are sentences a founder says before burning another 6 months. The criteria above are the boundary. The data is the data.

---

## Pre-mortem (written now, before we know)

Hypotheses for why this might fail, written before launch:

1. The aura trend is past peak. By the time we ship, the meme has cooled enough that organic Reels don't gain traction.
2. The format is too similar to existing Higher-or-Lower games to feel novel enough for word-of-mouth.
3. India distribution without local language and local creators is harder than estimated; cold DMs at 5% response rate yield insufficient seed creators.
4. The share card is good but not great. People play but don't post.
5. The founder doesn't have the time to ship 3 Reels/day for 8 weeks straight while running other things.
6. The mechanic is fun but the moments deck has cultural blindspots that turn off the target audience.
7. A competitor (Meta, an Indian app) ships a copy in 4 weeks and out-distributes us.

If, at the post-mortem, the actual failure mode is on this list, the original thesis was incomplete but at least we predicted it. If the failure mode is something we didn't list, we have something to learn for the next project.

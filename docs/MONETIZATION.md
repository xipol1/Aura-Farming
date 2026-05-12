# Monetization

## Principle

**No monetization in v1.** Adding pricing, ads, or upsells before PMF kills viral growth.

The first revenue dollar comes only after **10,000 Weekly Active Users**. Before that threshold, every cent of friction reduces K-factor more than it captures revenue.

---

## Revenue paths (in priority order)

### Path 1: Aura Off Pro (one-time purchase)

**Activate at**: 10,000 WAU
**Price**: ₹99 India / 2.99€ EU / $1.99 USD
**Format**: One-time, lifetime unlock. No subscription.

**What it unlocks**:
- Watermark-free share cards
- Custom card themes (5 alternative palettes beyond the auto-assigned tag palette)
- Personal stats dashboard (best streak per category, win rate vs. community average)
- "Sigma Verified" badge that appears on share cards
- Unlimited "skip this pair" (regular users get 1 skip per game)

**Why one-time, not subscription**:
- Indian Gen Z is sub-fatigued and skeptical of recurring charges.
- Lifetime unlocks feel honest and word-of-mouth-friendly.
- We don't have recurring infra costs that justify recurring revenue (yet).

**Payment processing**:
- **India**: Razorpay (UPI + cards + wallets). 2% per transaction, no monthly fee.
- **Global**: Stripe. 2.9% + $0.30.
- **Path of least resistance**: launch Razorpay-only initially since that's our primary market. Add Stripe when diaspora demand justifies.

**Projection** (rough):
- 10,000 WAU, 1% convert → 100 buyers × ₹99 = ₹9,900 (~110€) one-time.
- At 50,000 WAU, 2% convert → 1,000 buyers × ₹99 = ₹99,000 (~1,100€).
- This alone does not sustain a business. It's the first stake in the ground.

---

### Path 2: Sponsored items (branded scenarios)

**Activate at**: 25,000 WAU
**Format**: Brands pay to insert their item into the deck with a curated aura score and tag.

**Examples**:
- Boat Lifestyle: "Boat Rockerz at the gym" → +12,000 aura, `auntie_approved`
- Swiggy: "Ordering biryani at 2am during a deadline" → +27,000 aura, `chronically_online`
- Sugar Cosmetics: "Reapplying lipstick before a Zoom intro" → +8,400 aura, `main_character`

**Pricing**:
- Tier 1 (5 inserts, 30-day window): ₹50,000 (~570€)
- Tier 2 (1 insert, 30-day window): ₹15,000 (~170€)
- Tier 3 ("hero" item that always appears in the first 10 pairs): ₹150,000 (~1,700€)

**Why this works**:
- Native, non-disruptive ad format.
- Aligns with our content style (the item *is* the ad).
- Indian consumer brands are already buying meme content from creators — this is just a different format.

**Sales channel**: leverage existing Adflow infrastructure (founder's other startup) to source advertisers. Direct synergy.

**Disclosure**: every sponsored item must be marked with a small "ad" badge on its illustration to comply with India advertising standards (ASCI) and the SEBI influencer guidelines.

---

### Path 3: Themed decks

**Activate at**: 50,000 WAU
**Format**: Premium themed item packs purchasable for ₹49–₹99 each.

**Examples**:
- "Cricket Aura Off" — 100 cricket-only items, deep India cricket lore
- "Bollywood Aura Off" — 100 iconic Bollywood moments
- "JEE/IIT Aura Off" — student-life specific
- "Tech Bro Aura Off" — startup / corporate / VC moments

Players can switch decks. Streaks are tracked per deck.

**Pricing**:
- Single deck: ₹49
- All-decks pass: ₹199

**Why this works**:
- Players who like the game want more.
- Themed content has higher cultural specificity → more controversial → more share-worthy.
- Production cost is just our time + Claude Code.

---

### Path 4: Aura Off Live (events / tournaments)

**Activate at**: 50,000 WAU
**Format**: Monthly 24-hour tournament with public leaderboard. Sponsor anchors the event.

**Example**: "Boat × Aura Off Battle Royale — Sept 2026"
- 24-hour tournament window
- Top 100 streaks win merch (sponsor-provided)
- Sponsor gets logo + 2 branded items in the deck for the event

**Sponsor revenue**: ₹200,000–500,000 per event (~2,200–5,500€).

**Frequency**: max once per quarter to avoid event fatigue.

---

### Path 5: Aura Off Wrapped (annual)

**Activate at**: December 2026 if we've survived.
**Format**: Spotify Wrapped-style year-end personalized recap.

- Most-played category for you
- Your "aura personality type" (one of 8 archetypes)
- Your best streak vs. community
- Shareable summary card

**Monetization**:
- Sponsor a Wrapped category ("Brought to you by [Brand]")
- Premium Wrapped with extended stats (₹49 unlock)

---

## What we will not do

| Approach | Why not |
|---|---|
| Banner ads | Destroys the meme aesthetic. Low CPM at our scale. |
| Pop-up ads | Same, worse. |
| Mandatory video ads to continue playing | Kills the loop. Indian Gen Z will close the tab. |
| Paywalled streaks ("pay to revive") | Predatory. Damages brand. |
| NFTs / crypto | We are not desperate. |
| Selling user data | We have no PII to sell, and we wouldn't even if we did. |
| Pay-to-win in any form | The game is satire. Winning is meaningless. |

---

## Pricing in India context

Indian Gen Z accepts micro-pricing for digital goods when:
1. Value is obvious and immediate.
2. Payment method is frictionless (UPI is the gold standard).
3. Price is below the "cup of chai mental anchor" (~₹20) for impulse, or below "movie ticket" (~₹200) for premium.

Our ₹49–₹199 pricing band hits the impulse-to-premium spectrum without exceeding the chai anchor for the impulse purchases.

---

## Cost structure to plan against

When we cross monetization activation, infra costs jump:

| Threshold | Monthly cost | Service |
|---|---|---|
| 50k MAU | ~25€ | Supabase Pro |
| 100k MAU + high bandwidth | ~45€ | + Vercel Pro |
| 500k MAU | ~100-150€ | Scaling Supabase compute |

At 10k WAU and 1% Pro conversion, we cover Supabase Pro 4x over. Healthy unit economics start at the first activation threshold.

---

## Revenue tracking

Once monetization is live:

| Metric | Target |
|---|---|
| Aura Off Pro purchases / month | 100 → 1,000 over 6 months |
| Sponsored items signed / month | 1 → 5 |
| Themed deck purchases / month | 50 → 500 |
| Total MRR by month 12 | 5,000–15,000€ |

This is a side-project ceiling, not a venture ceiling. If MRR exceeds 15k€, evaluate whether to invest seriously vs. keep as cash cow.

---

## Investor / acquisition considerations (forward-looking)

This product is not designed to be VC-fundable. The realistic exit paths:

1. **Acquihire by a larger consumer app** (Meta, Roposo, Sharechat) interested in the format and audience.
2. **Acquisition of the IP / format** by an entertainment co (Voot, Hotstar) wanting a viral hook.
3. **Run indefinitely as a lifestyle business** with 2-5k€ MRR after costs.

For (1) and (2): keep the codebase clean, the metrics auditable, and the user data minimal (low integration cost for acquirer).

For (3): the side-project framing is permanent. Don't quit the day job for this.

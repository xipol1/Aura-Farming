# Content Guidelines

These rules apply to every addition or edit to `items.json`, `dilemmas.json`, `sass_responses.json`, and `modifiers.json`. They exist so the product stays funny without becoming defamatory, hateful, or stale.

---

## Items (cultural moments)

### Format

```json
{
  "id": "i_NNN",                          // sequential, never reused
  "label": "Short verbal description",    // max 60 chars including spaces
  "aura": 12345,                          // -200000 to +400000
  "tag": "main_character" | "sigma" | "cringe" | "npc" | "midwit" | "delusional" | "auntie_approved" | "chronically_online",
  "category": "cricket_india" | "football_global" | "cinema_iconic" | "anime_sigma" | "tech_founder" | "politics_soft" | "viral_moments" | "bollywood" | "music_culture" | "anti_aura" | "sigma_everyday" | "cringe_legendary",
  "cultural_anchor": "global" | "india" | "diaspora"
}
```

### Label rules

- Maximum 60 characters. Anything longer breaks the share card layout.
- Present tense or simple description. Not editorialized: "Messi raising the World Cup", not "Goat Messi finally raising the cup".
- Describes a public event, behavior, or pattern. Not a private fact.
- Real people are referenced for their public actions only, never private life.
- Patterns/archetypes (e.g. "LinkedIn motivational post about 5am hustle") are preferred when a specific identification would create defamation risk.

### Aura score rules

Score reflects how much "aura" the moment carries. Subjective + satirical. Guidance:

| Score range | Meaning |
|---|---|
| 250,000+ | Pantheon. Once-in-a-generation cultural moment. |
| 150,000–250,000 | Iconic. Universally recognized as "aura". |
| 50,000–150,000 | Strong positive aura. Niche-respected. |
| 5,000–50,000 | Mildly positive. Mid-good. |
| -5,000 to +5,000 | Neutral / unclear. Useful for tricky pairs. |
| -50,000 to -5,000 | Mid-bad. Cringe-adjacent. |
| -150,000 to -50,000 | Strong negative. Public second-hand embarrassment. |
| Below -150,000 | Aura zero. Career-ending public moments. |

When in doubt, score *lower* than your instinct says. Negative aura is funnier.

### Tag rules (visual identity)

The tag controls the share card's color palette. Pick the tag that *describes the energy* of the moment.

| Tag | Use when |
|---|---|
| `main_character` | Cinematic peaks, hero moments |
| `sigma` | Lone-wolf, principled, quiet power |
| `cringe` | Second-hand embarrassment, public humiliation |
| `npc` | Empty, default, conformist behavior |
| `midwit` | Confident but wrong, mediocre takes |
| `delusional` | Internally consistent but disconnected from reality |
| `auntie_approved` | South Asian elder-approved respectability |
| `chronically_online` | Internet-pilled, terminally online energy |

### Category rules

Used for themed decks and filtering. Pick the closest category, even if imperfect. Adding new categories requires updating the TypeScript types in `/lib/types.ts` and the validation script.

### Cultural anchor

| Anchor | Meaning |
|---|---|
| `global` | Recognizable to most internet-literate Gen Z worldwide |
| `india` | Requires India cultural context to land |
| `diaspora` | Requires Indian diaspora context (NRI life, immigrant parent jokes) |

---

## Dilemmas (impossible choice items)

### Format

```json
{
  "id": "d_NNN",
  "prompt": "Choose:",
  "option_a": { "label": "...", "aura_if_chosen": 12000, "tag": "..." },
  "option_b": { "label": "...", "aura_if_chosen": -8000, "tag": "..." },
  "category": "trolley" | "friends_vs_partner" | "life_choices" | "india_cultural" | "cringe_vs_cringe" | "sigma_extreme",
  "cultural_anchor": "global" | "india" | "diaspora"
}
```

### Label rules

- Maximum 70 characters per option.
- Each option must be *actually defensible by someone*. If one option is obvious, it's not a dilemma — it's a regular item.
- Avoid asymmetric power: both choices should feel costly.
- Personal stakes preferred over abstract trolley problems.

### Aura assignment rules

- The "correct" answer (higher aura) shouldn't be obvious. Sometimes the cowardly option scores higher than the brave one because it's actually wiser.
- Aim for a 30%–70% community split. If everyone picks the same option, it's not a dilemma.
- Negative scores are encouraged. Many dilemmas end with both options losing aura, just differently.

---

## Sass responses

### Format

```json
{
  "wrong_obvious": [ "string", "string", ... ],
  "wrong_close": [ ... ],
  "right_sleeper": [ ... ],
  "right_obvious": [ ... ],
  "dilemma_neutral": [ ... ],
  "game_over": [ ... ]
}
```

### Hard rules

1. **Length ≤ 12 words per line.** Strictly enforced by `validate-content.ts`.
2. **No attacks on identity**: gender, race, sexuality, religion, disability, age, body, nationality. Not even ironic.
3. **No self-harm references**, including dark jokes about killing/dying/suicide. Game-over context allows "RIP", "buried", "killed by", but only as game-state words.
4. **No real-person attacks by name**. "Andrew Tate giving advice" can be an *item*, but a sass line should never say "you're like Andrew Tate" — that's a personal attack on the player.
5. **No politically charged statements**. Avoid taking sides on partisan issues.
6. **No drug/alcohol promotion**. Aunties and uncles are fine.

### Soft rules

- Aim for 30% spanglish/hinglish code-switching ("bhai", "bro", "pero", "tu primo del Stanford").
- Specific is funnier than generic. "Your LinkedIn recruiter just unmatched" > "you lost".
- The voice is: dry, knowing, slightly tired, never cruel.
- Reference internet culture without explaining it. If you have to explain it, it's not landing.

### Reuse rules

- The `lib/sass.ts` picker avoids reusing a line within one game session.
- Game-over lines must work standalone on a share card. Test by reading the line out of context.

### Forbidden words list

(Maintained in `validate-content.ts` constants. Never use these in any line.)

- Slurs (any language)
- Suicide / kill yourself / kys / unalive
- Sexual content
- Specific real-person names as targets ("X is a clown")
- Disease names used as insults
- Body-shaming terms

---

## Modifiers

### Format

```json
{
  "id": "m_NNN",
  "text": "Today: ... (max 60 chars)",
  "affects_tag": "tag_name" | null,
  "affects_category": "category_name" | null,
  "multiplier": 2.0,
  "cosmetic_only": false,
  "cosmetic_effect": "effect_id"
}
```

- Either `affects_tag` or `affects_category` is set (or both). `null` means global.
- Multipliers between 0.5 and 2.0 for scoring effects. -1.0 is allowed for "inversion" modifiers.
- `cosmetic_only: true` modifiers don't change scoring; they trigger UI variants (e.g. pirate sass).

---

## When in doubt, omit

If an item or sass line feels like it might cause trouble (legal, brand, target audience), drop it. The deck is large enough that no single line is essential. Better to ship a smaller, cleaner deck than a larger one with landmines.

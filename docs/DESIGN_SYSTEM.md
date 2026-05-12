# Design System

## Visual identity

**Tone**: Brutalist + meme native. High-contrast, oversized type, monospace accents. Not Apple-clean. Not Material polite. Closer to early Yeezy merch crossed with terminal emulator.

**Anti-references**: rounded soft corners, pastel gradients, Lottie animations, drop shadows, "Stripe-style" SaaS aesthetic. Anything that screams "designed by a startup". This product looks made by someone who doesn't care about looking professional.

---

## Typography

| Role | Font | Weight | Size (mobile) |
|---|---|---|---|
| Display / numbers | Space Grotesk | 700 | 80–320px |
| Headlines | Space Grotesk | 700 | 32px |
| Body | Inter | 500 | 16px |
| Italic / sass | Inter | 500 italic | 18px |
| Code / URL | JetBrains Mono | 500 | 14px |

All from Google Fonts, all OFL-licensed.

Use `font-feature-settings: "tnum"` on all numeric displays to prevent jitter during count-up animations.

---

## Color tokens

### Base

```css
--bg-primary: #0a0a0a;        /* near black, default app bg */
--bg-elevated: #1a1a1a;       /* card surfaces, modals */
--text-primary: #ffffff;
--text-secondary: #999999;
--text-tertiary: #555555;
--border-subtle: #2a2a2a;
--accent-success: #00ffaa;    /* correct */
--accent-danger: #ff3344;     /* wrong */
```

### Tag palettes

These drive both the share card background and the in-game reveal flash. Each tag has a `bg`, `accent`, and `mood` description.

```ts
export const TAG_PALETTES = {
  main_character: { bg: '#1a0030', accent: '#ff3399', mood: 'cinematic_saturated' },
  sigma:          { bg: '#0a0a0a', accent: '#ffffff', mood: 'monochrome_austere' },
  cringe:         { bg: '#5a4a00', accent: '#a8a8a8', mood: 'drained_sickly' },
  npc:            { bg: '#3a3a3a', accent: '#666666', mood: 'flat_lifeless' },
  midwit:         { bg: '#2a3a5a', accent: '#88aaff', mood: 'mediocre_corporate' },
  delusional:     { bg: '#1a3a1a', accent: '#ffcc33', mood: 'bright_unhinged' },
  auntie_approved:{ bg: '#3a0a0a', accent: '#ffd700', mood: 'indian_opulence' },
  chronically_online: { bg: '#0a2a3a', accent: '#00ffaa', mood: 'internet_pilled' },
} as const;
```

---

## Spacing

8px base grid. Allowed values: `0, 4, 8, 12, 16, 24, 32, 48, 64, 96`. Anything else is a smell.

Tailwind config in `tailwind.config.ts`:

```ts
spacing: {
  '0': '0', '1': '4px', '2': '8px', '3': '12px',
  '4': '16px', '6': '24px', '8': '32px',
  '12': '48px', '16': '64px', '24': '96px',
}
```

---

## Components

### Buttons

Two variants only.

**Primary** (HIGHER, LOWER, RESTART):
- Full width
- Height: 64px on mobile, 56px tablet+
- Background: `var(--text-primary)` (white)
- Text: `var(--bg-primary)` (black)
- Font: Space Grotesk Bold 24px, uppercase, tracked +0.05em
- Border: none
- Rounded: 0 (no rounded corners — brutalist)
- Active state: invert (black bg, white text)

**Ghost** (Share, Copy link, sound toggle):
- Background: transparent
- Border: 2px solid `var(--text-primary)`
- Same dimensions and font as primary
- Active: fills with white, text black

No tertiary buttons. No icon-only buttons. Text always.

### Item Panel

```
┌──────────────────────────┐
│                          │
│   [optional illustration │
│      SVG, 80×80px]       │
│                          │
│   MESSI RAISING          │  ← Item label, Space Grotesk 700, 28px
│   THE WORLD CUP           │
│                          │
│   +234,000 aura          │  ← Score, Space Grotesk 700, 56px, accent color
│                          │
└──────────────────────────┘
```

Or on the hidden side (right panel):

```
┌──────────────────────────┐
│                          │
│   [optional illustration]│
│                          │
│   HIDE THE PAIN          │
│   HAROLD                 │
│                          │
│      ???                 │  ← Placeholder, 56px, accent color
│                          │
└──────────────────────────┘
```

Both panels: full width on mobile, stacked vertically. On tablet+, side by side.

### Reveal animation

When user taps HIGHER/LOWER:
1. The "???" on the right panel begins counting up to the actual score.
2. Duration: 800ms.
3. Easing: `cubic-bezier(0.34, 1.56, 0.64, 1)` (overshoot + settle).
4. Color flash on the screen edge: green (correct) or red (wrong).
5. Final number color matches the killer item's tag accent.

### Sass line

Appears below the panels after reveal, fades in from y+20px to y+0 over 300ms.
Inter Medium Italic, 18px, `var(--text-secondary)`.
Persists until next pair or game-over screen.

### Streak counter

Top of the play screen.

```
🔥 24
```

Flame emoji (the only emoji allowed in the UI), Space Grotesk Bold 32px.
On increment, scale animation 1 → 1.2 → 1 over 200ms.

### Modifier badge

Top-right corner of play screen. Pill shape (the only rounded element in the entire UI, justified because pills read as "interactive label").

```
TODAY: CRICKET ×2
```

Background: tag accent of currently active modifier. Text: black.
Tappable to re-read the modifier modal.

---

## Iconography

**Avoid icons entirely** wherever possible. Use text labels.
Allowed exceptions:
- Flame emoji 🔥 for streak counter
- Native share icon on Share button (platform-rendered, we don't ship our own)

No icon library. No Lucide. No Heroicons. The aesthetic is anti-iconography.

---

## Illustrations

For each of the 8 tags, one SVG illustration in `/content/illustrations/`. Style:

- **Monoline**: 4px stroke, no fill
- **Color**: tag accent color
- **Style reference**: meme stickers, GigaChad simplified, "Wojak" minimalist
- **Size**: 80×80px viewBox, scalable
- **Original**: drawn by us or generated by Claude Code in SVG. Never copy other illustrators.

Examples:
- `main_character.svg`: side profile silhouette with sunglasses
- `sigma.svg`: lone wolf at mountaintop
- `cringe.svg`: an "umm actually" finger
- `npc.svg`: featureless face
- `midwit.svg`: bell-curve guy in the middle
- `delusional.svg`: a halo over an empty stick figure
- `auntie_approved.svg`: a tea cup
- `chronically_online.svg`: smartphone with WiFi waves

Illustrations are **optional decoration**. The text label is the primary signifier. If the SVG fails to load, the design must still work.

---

## Motion principles

1. **Animations are functional, not decorative.** Every motion communicates state change.
2. **Default duration**: 200–300ms. Reveal is the exception at 800ms (intentional drama).
3. **Easing**: `ease-out` for entrances, `ease-in` for exits, overshoot for celebrations.
4. **No parallax. No scroll-driven animations. No micro-interactions on hover** (this is a mobile-first app, hover doesn't exist).
5. **Respect `prefers-reduced-motion`**: replace all motion with fade-only.

---

## Tone of voice (copy)

- **Default**: dry, sarcastic, knowingly chronically online.
- **Forbidden**: corporate ("amazing!", "awesome!"), apologetic ("oops!"), gendered or identity-based jabs, self-harm references, hard politics.
- **Allowed**: light Gen Z self-deprecation, code-switching English/Hindi/Spanglish, references to internet culture, dark humor that punches at concepts not people.

Examples of *good* copy:
- "Your password is probably your birthday."
- "Even your auntie would do better."
- "Bro fr picked Skibidi over John Wick."
- "Sigma pick. Pocos lo hubieran visto."

Examples of *bad* copy:
- "Yay! You got it!" (too corporate)
- "[group identity] always pick X" (identity-based)
- "Kill yourself lol" (never)
- "[Real person's name] is a clown" (defamation)

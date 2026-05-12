# Share Card Spec

The share card is the most important component in this project. If it does not work, the product does not work.

## Purpose

When a player dies, we generate a vertical PNG image (1080×1920) that summarizes their run. The player downloads or shares this image. **The image must be self-contained and meme-able with zero context.** A stranger seeing it in a WhatsApp group should understand it in 2 seconds and want to click the link.

---

## Dimensions

- **Aspect ratio**: 9:16 (vertical).
- **Resolution**: 1080 × 1920 px (the standard for Reels, Shorts, Stories).
- **Safe zone**: 80px margin top + bottom (avoids platform UI overlap when posted as Story/Reel).
- **Format**: PNG, sRGB, < 500 KB.

---

## Anatomy (top to bottom)

```
┌──────────────────────────────────────┐ ← 1080 px
│                                      │
│           AURA OFF                   │  ← Wordmark, 56px, top center
│           ───────                    │
│                                      │
│                                      │
│              87                      │  ← Streak. MASSIVE. 320px font.
│            STREAK                    │  ← Label below, 48px
│                                      │
│        ──────────────                │
│                                      │
│        Died comparing                │  ← Sub-headline, 36px
│                                      │
│      ╔═════════════════╗             │
│      ║                 ║             │
│      ║  HIDE THE PAIN  ║             │  ← Left item, in a card
│      ║     HAROLD      ║             │
│      ║   aura: 89,400  ║             │
│      ║                 ║             │
│      ╚═════════════════╝             │
│                                      │
│              VS                      │  ← Connector
│                                      │
│      ╔═════════════════╗             │
│      ║                 ║             │
│      ║  ANDREW TATE    ║             │
│      ║  LIFE ADVICE    ║             │
│      ║   aura: -47,200 ║             │
│      ║                 ║             │
│      ╚═════════════════╝             │
│                                      │
│        ──────────────                │
│                                      │
│   "Your password is probably         │  ← Sass line, italic, 42px
│    your birthday."                   │
│                                      │
│   ── Aura Off                        │
│                                      │
│                                      │
│   Beat my streak →                   │  ← CTA, 36px
│   auraoff.app                        │  ← URL, 28px, mono
│                                      │
└──────────────────────────────────────┘
```

---

## Color palette (driven by killer item's tag)

The card's background and accent color are determined by the **tag of the item that killed the player** (the one whose score was hidden, the "right" panel).

| Tag | Background | Accent | Mood |
|---|---|---|---|
| `main_character` | `#1a0030` (deep purple) | `#ff3399` (hot pink) | Cinematic, saturated |
| `sigma` | `#0a0a0a` (near black) | `#ffffff` (white) | Monochrome, austere |
| `cringe` | `#5a4a00` (mustard) | `#a8a8a8` (gray) | Drained, sickly |
| `npc` | `#3a3a3a` (mid gray) | `#666666` (lighter gray) | Flat, lifeless |
| `midwit` | `#2a3a5a` (slate blue) | `#88aaff` (washed blue) | Mediocre, corporate |
| `delusional` | `#1a3a1a` (deep green) | `#ffcc33` (gold) | Bright but unhinged |
| `auntie_approved` | `#3a0a0a` (deep maroon) | `#ffd700` (gold) | Indian opulence |
| `chronically_online` | `#0a2a3a` (cyber teal) | `#00ffaa` (toxic green) | Internet-pilled |

Background is solid color (no gradient — it costs file size and looks dated).
Text is always white on the dark bg, except where the accent color is used (numbers, headers).

---

## Typography

- **Wordmark / numbers**: Space Grotesk Bold (700) or Inter Black (900). Both are open-source on Google Fonts.
- **Body text**: Inter Medium (500).
- **Sass quote**: Inter Italic Medium.
- **URL**: JetBrains Mono Medium (also free).

Numbers must use tabular-nums to avoid jitter on the count-up animation when rendered live (this card is also rendered in the live game-over screen).

---

## Implementation

### Two layers of rendering

1. **Live screen at game over**: rendered as SVG component (`ShareCardSVG.tsx`) inside the React tree. This is what the player sees animated.
2. **Download/share image**: same SVG, serialized via `XMLSerializer`, drawn onto an HTMLCanvasElement at 1080×1920, exported as PNG blob, then handed to Web Share API or download link.

Both paths share the same component. The canvas path uses `useRef` + `html-to-image` library (12 KB, allowed) or pure DOM rasterization.

### File: `components/share/ShareCardSVG.tsx`

```tsx
type Props = {
  streak: number;
  killerLeft: Item;
  killerRight: Item;
  sassLine: string;
  paletteFromTag: ItemTag;
};

export function ShareCardSVG({ streak, killerLeft, killerRight, sassLine, paletteFromTag }: Props) {
  const palette = TAG_PALETTES[paletteFromTag];
  return (
    <svg viewBox="0 0 1080 1920" xmlns="http://www.w3.org/2000/svg">
      <rect width="1080" height="1920" fill={palette.bg} />
      {/* Wordmark */}
      <text x="540" y="160" textAnchor="middle" fontSize="56" fontFamily="Space Grotesk" fontWeight="700" fill="white">AURA OFF</text>
      {/* Streak */}
      <text x="540" y="540" textAnchor="middle" fontSize="320" fontFamily="Space Grotesk" fontWeight="700" fill={palette.accent} fontFeatureSettings='"tnum"'>{streak}</text>
      <text x="540" y="620" textAnchor="middle" fontSize="48" fontFamily="Inter" fontWeight="500" fill="white">STREAK</text>
      {/* ... etc ... */}
    </svg>
  );
}
```

### File: `components/share/ShareCardCanvas.tsx`

Wraps the SVG, provides a "Download PNG" button. Uses `html-to-image` to rasterize.

```tsx
export async function shareCardAsPng(svgElement: SVGSVGElement): Promise<Blob> {
  const dataUrl = await toPng(svgElement, { width: 1080, height: 1920, pixelRatio: 2 });
  const res = await fetch(dataUrl);
  return res.blob();
}
```

---

## Share flow

```ts
async function handleShare(gameId: string) {
  const blob = await shareCardAsPng(svgRef.current);
  const file = new File([blob], 'aura-off.png', { type: 'image/png' });

  if (navigator.canShare?.({ files: [file] })) {
    await navigator.share({
      files: [file],
      title: 'Beat my Aura Off streak',
      text: `I scored ${streak}. Try it: https://auraoff.app/g/${gameId}`,
      url: `https://auraoff.app/g/${gameId}`,
    });
    trackShare('native');
  } else {
    // Fallback: download + copy link
    downloadBlob(blob, 'aura-off.png');
    await navigator.clipboard.writeText(`https://auraoff.app/g/${gameId}`);
    trackShare('copy_link');
  }
}
```

---

## OG image for `/g/[id]` public page

The public game-over page must have an Open Graph image so WhatsApp/Twitter/IG previews look right.

Use Next.js's built-in dynamic OG image generation:

```tsx
// app/g/[id]/opengraph-image.tsx
import { ImageResponse } from 'next/og';

export const runtime = 'edge';
export const size = { width: 1200, height: 630 };
export const contentType = 'image/png';

export default async function OG({ params }: { params: { id: string } }) {
  const game = await fetchGame(params.id);
  return new ImageResponse(
    (
      <div style={/* mimics share card layout, 1200×630 */}>
        ...
      </div>
    ),
    size
  );
}
```

Note: OG uses 1200×630 (landscape), not 1080×1920. Two different layouts:
- The downloaded share card (1080×1920) is for posting to Stories/Reels.
- The OG image (1200×630) is for link previews when the URL is pasted.

Both pull from the same data, different layouts.

---

## Quality bar

Before launch, run this checklist on at least 5 share cards generated from real game-overs:

- [ ] Wordmark legible at thumbnail size (200×356 preview in IG inbox)
- [ ] Streak number is the dominant visual element
- [ ] Sass line readable but not crowding the layout
- [ ] URL is legible
- [ ] Color palette matches the killer item's tag
- [ ] No text overflow on any item label up to 32 characters
- [ ] File size < 500 KB
- [ ] Looks intentional, not generated
- [ ] Beats the "would you forward this to a friend?" test on at least 3 of 5 reviewers

---

## Common pitfalls

| Pitfall | Mitigation |
|---|---|
| Long item labels break the layout | Truncate at 32 chars with ellipsis. Validate JSON at build time. |
| Sass line too long for the box | Word limit 12 enforced in `validate-content.ts` |
| Streak goes to 4 digits and overflows | Reduce font size from 320 to 240 at streak ≥ 100 |
| Fonts not loaded when rasterizing | Pre-load fonts before opening game-over screen |
| OG image cache stale | Use `revalidate = false`, regenerate on each request (edge runtime is cheap) |
| Card looks great on iPhone, broken on Android | Test on real Chrome Android + Samsung Internet before launch |

# Design System — Should I Buy It? Indian Edition

Source of truth for UI. Extracted from current `index.html`. Follow this, don't invent new styles.

## Direction
Playful desi neo-brutalism: creamy paper, bold black outlines, hard offset shadows, sticker badges, rotating emoji collage, Hinglish microcopy. Fun but readable. Slightly larger type throughout (labels/subs/inputs bumped +1px from the original scale).

## Colors
```css
--orange:#E86A2C;  /* headlines, focus */
--green:#1DB954;   /* bar gradient start */
--green-dark:#0E9E44; /* CTA bg, logo, footer, PNG accents */
--blue:#4A90D9;
--pink:#FF6B9D;    /* stickers, idea tile */
--yellow:#FFC93C;  /* highlights, big hours */
--purple:#6C5CE7;  /* idea send button */
--ink:#1E1E1E;
--paper:#FFFDF7;   /* body bg + PNG bg */
--card:#FFFFFF;
```
Usage: body `var(--paper)`, headings `var(--orange)`, CTA `var(--green-dark)` white text (large-text AA), footer `var(--green-dark)` white text, stats bg `#F7F3E9`, equiv bg `#FFF6D6`, seg bg `#F1EFE9`.

## Typography
- Fonts: `Baloo 2` (700/800 headings, labels) + `Fredoka` + `system-ui,sans-serif` fallback. `fonts.gstatic.com` preconnected.
- H1 (hero): `clamp(32px,6.5vw,56px) / 0.95 / 800` desktop; `clamp(26px,8.5vw,40px)` under 560px, color orange
- Card H2: `24px / 1.2 / 800`
- Labels: `15px / 800`; `.sub`: `15px / 600 #666`; inputs: `17px / 700`
- Big hours: `clamp(44px,8vw,68px) / 800` yellow on `#111`
- Pill / idea tile: `14px`; chips `14px`; seg `15px`; CTA `20px/800`
- Stats value `20px`, captions `13px`; equiv `15px`; hist items `15px`; footer `14px`

## Spacing
8px-based: cards `24px` padding, `24px` grid gap, inputs `13-14px`, chips `7px 13px`, sections `12-18px` vertical. Page max `1100px` hero, `1080px` main, `24px` side padding.

## Radius & Shadows
- Cards: `22px` radius, `3px solid #111`, `8px 8px 0 #111`
- Inputs: `14px` radius, `2.5px solid #111`; focus: `border orange + 3px 3px 0 orange`
- Chips/buttons: `999px` pills / `16px` CTA / `12-14px` secondary; `2-3px solid #111` + `2-5px hard shadow`
- CTA press: `translate(3px,3px)` + shadow `1px 1px 0 #111`
- Items (collage): `50%` circle, `3px` border, `0 6px 0 rgba(0,0,0,.08)`
- Modal: `.feature-card` reused at `min(560px,94vw)`, `90vh` max with scroll

## Components
- **Topbar** (`header.topbar`): logo bubbles (white pills, 2.5px green-dark border, ±2deg rotate) + `.top-actions` (black pill `🇮🇳 Indian Edition • ₹` + pink `.idea-tile 💡 Idea?`); wraps right-aligned under 560px
- **Hero ring** (`.circle-stage` 560px max): `.ring` 90s spin, `.item` 84px (52px small, 62/40px under 560px), color variants, inner `<i>` counter-spin, `aria-hidden`
- **Cards** (`.card`): two equal-height flex columns
- **Seg toggle** (`.seg`): `#F1EFE9` pill, active button black bg white text
- **Chips** (`.chip`): white, hover yellow + lift, wrap
- **CTA** (`.calc-btn`): green-dark, `20px/800`, full width
- **Result**: `.big-number` black card yellow hours (`overflow-wrap` on labels); `.verdict` dashed border; `.stats` 3-col; `.bar-wrap` 18px pill with green-yellow-orange gradient (CSS 3px min); `.equiv` cream box
- **Empty badges**: 5 tilted emoji circles (cream/green accents, hard shadows), wrapping flex
- **Actions**: `.btn2` + `.green` (Save Hisab) + default (Copy)
- **History**: `#histList` max-height 230px scroll, `.hist-item` + 36px-min ↩ button with `aria-label`
- **Sticker**: absolute `-16px` top pink 6deg (input card); **inline** pill variant (`.modal-sticker`) in the feature modal
- **Modals**: `.modal-overlay` dim + `.modal-close` circle button; `.disc-list` borderless bullet stack
- **Dots**: 5 fixed pastel dots

## States
- Empty: badge row + `Abhi kuch calculate nahi kiya` (default first paint — no auto-calc)
- Result: `#resultBox display:block`, bar animates 0→pct 0.8s (off under reduced-motion)
- Hover: chips yellow, CTA lift; Active: CTA sink; Focus: orange ring on inputs + `:focus-visible` everywhere
- Sending… / Sent / fallback / throttle-hold (idea form); Copied ✅ / Saved ✅ confirmations
- Disabled: send button while posting only
- Error: `alert('Price daalo yaar!')` for empty/invalid price; no inline errors

## Responsive Rules
- Desktop >860px: `main` 2-col grid
- ≤860px: 1-col, gap 24px
- ≤480px: `.row2` 1-col, stats stay 3-col (check 320px no overflow)
- ≤560px: hero padding `80px`, smaller badges/type, nowrap pill, topbar wraps
- Hero: `min(560px,92vw)` square, center padding `0 98px` desktop (overlap-safe)

## Accessibility
- Labels on all inputs (`for` + `required` where needed), `min/max` on numbers, keyboard Enter calculates
- Contrast: white on green-dark CTA/footer passes large-text AA; `#666` sub on white ok; errors never color-only
- Focus trapped in open modals (`trapTab`), Esc/overlay close, focus moved in/out deliberately
- Ring emojis `aria-hidden`; history reload has `aria-label`; heading order H1→H2→H3
- `prefers-reduced-motion` disables spin + bar animation

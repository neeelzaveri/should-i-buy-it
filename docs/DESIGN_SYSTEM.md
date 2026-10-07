# Design System — Should I Buy It? Indian Edition

Source of truth for UI. Extracted from `index.html:9-191`. Follow this, don't invent new styles.

## Direction
Playful desi neo-brutalism: creamy paper, bold black outlines, hard offset shadows, sticker badges, rotating emoji collage, Hinglish microcopy. Fun but readable.

## Colors
```css
--orange:#E86A2C;  /* headlines, focus */
--green:#1DB954;   /* primary CTA */
--green-dark:#0E9E44; /* logo, footer */
--blue:#4A90D9;
--pink:#FF6B9D;    /* sticker */
--yellow:#FFC93C;  /* highlights, big hours */
--purple:#6C5CE7;
--ink:#1E1E1E;
--paper:#FFFDF7;   /* body bg */
--card:#FFFFFF;
```
Usage: body `var(--paper)`, headings `var(--orange)`, CTA `var(--green)` white text, footer `var(--green-dark)` white text, stats bg `#F7F3E9`, equiv bg `#FFF6D6`, seg bg `#F1EFE9`.

## Typography
- Fonts: `Baloo 2` (700/800 headings, labels) + `Fredoka` (body fallback) + `system-ui,sans-serif` fallback. Loaded in `index.html:8`.
- H1 (hero): `clamp(32px,6.5vw,56px) / 0.95 / 800`, color orange
- Card H2: `22px / 1.2 / 800`
- Labels: `14px / 800`; `.sub`: `14px / 600 #666`; inputs: `16px / 700`
- Big hours: `clamp(44px,8vw,68px) / 800` yellow on `#111`
- Pill: `13px / 700` white on `#111`, radius 999px

## Spacing
8px-based: cards `24px` padding, `24px` grid gap, inputs `13-14px`, chips `6px 12px`, sections `12-18px` vertical. Page max `1100px` hero, `1080px` main, `24px` side padding.

## Radius & Shadows
- Cards: `22px` radius, `3px solid #111`, `8px 8px 0 #111`
- Inputs: `14px` radius, `2.5px solid #111`; focus: `border orange + 3px 3px 0 orange`
- Chips/buttons: `999px` pills / `16px` CTA / `12-14px` secondary; `2-3px solid #111` + `2-5px hard shadow`
- CTA press: `translate(3px,3px)` + shadow `1px 1px 0 #111`
- Items (collage): `50%` circle, `3px` border, `0 6px 0 rgba(0,0,0,.08)`

## Components
- **Topbar** (`header.topbar`): logo bubbles (white pills, 2.5px green-dark border, ±2deg rotate) + black pill `🇮🇳 Indian Edition • ₹`
- **Hero ring** (`.circle-stage` 560px max): `.ring` 90s spin, `.item` 84px (52px small), color variants `c-blue/pink/purple/orange/yellow/green/white`, inner `<i>` counter-spin
- **Cards** (`.card`): two equal-height flex columns
- **Seg toggle** (`.seg`): `#F1EFE9` pill, active button black bg white text
- **Chips** (`.chip`): white, hover yellow + lift
- **CTA** (`.calc-btn`): green, `19px/800`, full width
- **Result**: `.big-number` black card yellow hours; `.verdict` dashed border; `.stats` 3-col; `.bar-wrap` 18px pill with green-yellow-orange gradient; `.equiv` cream box
- **Actions**: `.btn2` + `.green` (#25D366 for WhatsApp)
- **History**: `#histList` max-height 230px scroll, `.hist-item` white pill + black ↩ button
- **Sticker**: absolute `-16px` top, pink, 6deg rotate
- **Dots**: 5 fixed pastel dots

## States
- Empty: `.result-empty` `🛵☕📱👟⏰` centered
- Result: `#resultBox display:block`, bar animates 0→pct 0.8s
- Hover: chips yellow, CTA lift; Active: CTA sink; Focus: orange ring on inputs; Focus-visible must remain
- Disabled: not used — validate via clamping + alert
- Error: `alert('Price daalo yaar!')` for empty price; no inline errors V1

## Responsive Rules
- Desktop >860px: `main` 2-col grid
- ≤860px: 1-col, gap 24px
- ≤480px: `.row2` 1-col, stats stay 3-col (check 320px no overflow)
- Hero: `min(560px,92vw)` square, center padding `0 90px`

## Accessibility
- Labels on all inputs, `min/max` on numbers, keyboard Enter triggers calc (`index.html:465-467`)
- Contrast: #111 on white/cream/yellow passes; white on green CTA + green-dark footer passes; `#666` sub on white is decorative only
- Don't rely on color alone: verdict includes emoji + bold text + hours
- Keep heading order: H1 hero → H2 cards → H3 history
- Alt/emoji: emojis are decorative collage; core info is text

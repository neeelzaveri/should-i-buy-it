# CODE_STYLE.md — Should I Buy It? Indian Edition

## 1. Purpose
Single-file vanilla conventions. New code follows `index.html` patterns unless this file says otherwise.

## 2. Stack (actual)
- HTML5, CSS3 (custom properties, grid/flex), Vanilla JS ES6,Canvas 2D (hand-drawn share image)
- No TypeScript, React, Next.js, Tailwind, libraries, ESLint, Prettier config
- Fonts: Baloo 2 + Fredoka via Google Fonts (+ gstatic preconnect)
- Verify manually: open `index.html`, no build step

## 3. General Principles
- Readable over clever. One file → keep sections ordered: `<style>`, header/topbar, hero, `main` left/right, footer, modals, `<script>` helpers → ring → presets → mode → verdict/equiv → calculate → history → share builders → modals wiring.
- Reuse classes/IDs before creating new. Small helpers (`$`, `fmtIN`, `rr`, `wrapLines`) over libraries.
- No unnecessary abstractions; no duplicate button/card/modal styles.
- Keep Hinglish copy playful but consistent (₹, ghante, hisab, paisa-vasool).
- Placeholders hint, JS fallbacks default — keep both in sync when touching inputs.

## 4. Naming
- Files: `index.html` lowercase; docs `UPPER_SNAKE.md` (PRD.md, APP_FLOW.md...)
- CSS classes: `kebab-case` (`.topbar`, `.circle-stage`, `.calc-btn`, `.big-number`, `.hist-item`, `.modal-overlay`, `.disc-list`); vars `--kebab` (`--green-dark`, `--paper`)
- IDs: `camelCase` or short (`product`, `price`, `salary`, `hourlyHrs`, `calcBtn`, `resultBox`, `salaryBar`, `histList`, `featModal`, `discModal`, `waBtn`, `copyBtn`)
- JS: `camelCase` (`calculate`, `verdict`, `equivalents`, `renderHist`, `buildHisabText`, `drawHisab`, `hisabPng`, `trapTab`, `fmtIN`, `hourlyRate`, `effectiveRate`, `lastHisab`, `lastFeatSentAt`); consts `UPPER_SNAKE` (`CREATOR_EMAIL`)
- Booleans read clearly: `saveHistory`, `noLoan` (current `mode==='monthly'` is fine, keep style on edit)

## 5. Components / Structure
- No React components. DOM blocks = header + hero + `.card` left (input) + right (result) + footer + 2 modals.
- Keep UI, math, storage separated: `calculate()` orchestrates; `verdict()`/`equivalents()` pure; `renderHist()` renders only; share builders read `lastHisab` snapshot.
- Handle empty (`#emptyState`) vs result (`#resultBox`), sending/sent/fallback/throttle, copied/saved, validation alert, history empty text.

## 6. Formatting
- No Prettier/ESLint — match existing: 2-space indent, double quotes in HTML, single quotes in JS, semicolons, `const`/`let`, arrow `=>` for short helpers.
- Do not reformat unrelated blocks. Keep imports (only Fonts `<link>`) at top.
- Remove unused vars/imports, `console.log`, dead commented code.

## 7. Comments
WHY not WHAT.
Good:
```js
// Clamp savePct 5-100: 0% would divide by zero, >100% inflates rate
// FormSubmit returns HTTP 200 with success:"false" on file:// — must check body, not just status
```
Avoid:
```js
// Increment count
count++;
```
Keep `SAFE SINK` guards on `innerHTML` sites; explain thresholds in `verdict()` if changing.

## 8. Before Finishing
- Open `index.html` via double-click + mobile emulation; run TESTING.md critical journey
- Check 360px/1100px, Enter-key, presets, history, PNG save, copy, idea modal, resize, XSS probe
- No `console.log`, no dead CSS/JS, no new deps, docs updated if formulas/copy changed

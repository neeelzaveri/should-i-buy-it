# CODE_STYLE.md — Should I Buy It? Indian Edition

## 1. Purpose
Single-file vanilla conventions. New code follows `index.html` patterns unless this file says otherwise.

## 2. Stack (actual)
- HTML5, CSS3 (custom props, grid/flex), Vanilla JS ES6
- No TypeScript, React, Next.js, Tailwind, ESLint, Prettier config
- Fonts: Baloo 2 + Fredoka via Google Fonts
- Verify manually: open `index.html`, no build step

## 3. General Principles
- Readable over clever. One file → keep sections ordered: `<style>`, hero/topbar, `main` left/right, footer, `<script>` helpers → ring → presets → mode → verdict/equiv → calculate → history → bindings.
- Reuse classes/IDs before creating new. Small helpers (`$`, `fmtIN`) over libraries.
- No unnecessary abstractions; no duplicate button/card styles.
- Keep Hinglish copy playful but consistent (₹, ghante, hisab, paisa-vasool).

## 4. Naming
- Files: `index.html` lowercase; docs `UPPER_SNAKE.md` (PRD.md, APP_FLOW.md...)
- CSS classes: `kebab-case` (`.topbar`, `.circle-stage`, `.calc-btn`, `.big-number`, `.hist-item`); vars `--kebab` (`--green-dark`, `--paper`)
- IDs: `camelCase` or short (`product`, `price`, `salary`, `hrsPerDay`, `daysPerMonth`, `savePct`, `calcBtn`, `resultBox`, `salaryBar`, `histList`)
- JS: `camelCase` (`calculate`, `verdict`, `equivalents`, `renderHist`, `fmtIN`, `hourlyRate`, `effectiveRate`); consts `UPPER_SNAKE` if added (`MAX_HIST = 5`)
- Booleans read clearly: `isMonthly`, `hasHistory` (current `mode==='monthly'` is fine, keep style on edit)

## 5. Components / Structure
- No React components. DOM blocks = `.card` left (input) + right (result) + `.hero` + `footer`.
- Keep UI, math, storage separated: `calculate()` orchestrates; `verdict()`/`equivalents()` pure; `renderHist()` renders only.
- Props → function args with JSDoc if complex. Handle empty (`#emptyState`) vs result (`#resultBox`), validation alert, history empty text.

## 6. Formatting
- No Prettier/ESLint — match existing: 2-space indent, double quotes in HTML, single quotes in JS, semicolons, `const`/`let`, arrow `=>` for short helpers.
- Do not reformat unrelated blocks. Keep imports (only Fonts `<link>`) at top.
- Remove unused vars/imports, `console.log`, dead commented code.

## 7. Comments
WHY not WHAT.
Good:
```js
// Clamp savePct 5-100: 0% would divide by zero, >100% inflates rate
```
Avoid:
```js
// Increment count
count++;
```
Keep ring/preset/calc section comments short; explain thresholds in `verdict()` if changing.

## 8. Before Finishing
- Open `index.html` via double-click + mobile emulation; run TESTING.md critical journey
- Check 360px/1100px, Enter-key, presets, history, WhatsApp/Copy fallback
- No `console.log`, no dead CSS/JS, no new deps, docs updated if formulas/copy changed

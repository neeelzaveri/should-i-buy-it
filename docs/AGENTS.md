# Agent Instructions — Should I Buy It? Indian Edition

## Project Context
Single-file static app (`index.html`). Vanilla HTML/CSS/JS, no framework, no TypeScript, no Tailwind, no backend, no DB server. Hinglish, Indian ₹, mobile-first neo-brutalist design. All logic client-side + `localStorage`.

## Before You Start
- Read `docs/PRD.md` (what we build)
- Read `docs/ARCHITECTURE.md` (how it connects — static only)
- Read `docs/DESIGN_SYSTEM.md` (how it looks — do not restyle randomly)
- Read `docs/TRD.md`, `docs/APP_FLOW.md` for formulas and flows
- Inspect `index.html` fully before changing — there is only one source file
- For security/data/API changes also read `docs/SECURITY.md`, `docs/DATABASE.md`, `docs/API.md`

## General Rules
- Keep it a single `index.html` unless user explicitly asks for split/build setup.
- Do not add npm, webpack, React, Next.js, Tailwind, TypeScript, Prisma, Supabase unless requested.
- Reuse existing CSS classes, IDs, and JS helpers (`$`, `fmtIN`, `calculate`, `verdict`, `equivalents`, `renderHist`).
- Follow existing folder shape: `index.html` + `docs/`. Do not invent `/app`, `/components`, `/lib` for this project.
- Ask before major architecture changes (adding backend, auth, router, state library).
- Preserve Hinglish tone and Indian presets. Don't genericize to USD.

## Code Guidelines
- HTML: keep semantic sections (`header.topbar`, `.hero`, `main`, `footer`); keep IDs stable (`product`, `price`, `salary`, `calcBtn`, `resultBox`, etc.)
- CSS: use `:root` vars (`--orange`, `--green`, `--ink`, etc.); 2.5–3px `#111` borders; hard shadows (`8px 8px 0 #111`); keep breakpoints 860px / 480px
- JS: vanilla ES6, `const $ = id => document.getElementById(id)`; `camelCase` functions; guard `Number()` with `||0` + `Math.max/min`; keep `fmtIN` for en-IN formatting
- No new dependencies via CDN without approval (except fonts already used)
- Explain WHY in comments, not WHAT. Remove dead code and `console.log` before finishing

## Design Rules
- Follow `docs/DESIGN_SYSTEM.md` tokens exactly: colors, fonts (Baloo 2 + Fredoka), 8px-based spacing, radius, shadows
- Reuse `.card`, `.chip`, `.seg`, `.calc-btn`, `.stat`, `.btn2`, `.hist-item` — don't create duplicate button/card styles
- Handle all states: `#emptyState` vs `#resultBox`, loading (instant, no spinner needed), validation alert, mobile overflow
- Test responsive at 360px, 768px, 1100px after UI change

## Security Rules
- Never add API keys, secrets, or tracking pixels. No `.env` needed for this app.
- All inputs are user-local; always clamp/validate numbers client-side (see SECURITY.md)
- `localStorage` only for `sibi_hist` — never store passwords, tokens, salary to server (there is no server)
- Use `encodeURIComponent` for wa.me text; never inject raw HTML — use `textContent` except intentional `innerHTML` for verdict/equiv which is built from numbers + static strings
- Verify no PII leaves browser; share only on explicit button click

## Commands
```bash
# No install/build needed. To preview:
# Windows: double-click index.html OR
start index.html
# Or serve statically:
npx serve .
```
- No `npm run lint/test`. Verify manually: open file, test calculate, presets, history, share, resize.
- If you add tooling, document it here and update TRD.md.

## Boundaries
- Do NOT add authentication, backend API, database server, payments, or analytics without explicit approval + doc updates.
- Do NOT change calculation formulas, verdict thresholds, or equivalents without updating TRD.md + TESTING.md.
- Do NOT restyle brand (colors/fonts/shadows) without updating DESIGN_SYSTEM.md.
- If docs conflict with `index.html`, flag conflict before major change — code truth wins until docs fixed.

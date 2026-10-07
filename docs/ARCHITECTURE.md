# Architecture — Should I Buy It? Indian Edition

## System Overview
```
User
 ↓
index.html (HTML + CSS + JS, single file)
 ↓
Browser APIs: localStorage (sibi_hist) | Clipboard API | Canvas PNG download
External: Google Fonts CDN | FormSubmit AJAX (feature ideas, on explicit Send only)
No backend. No database server. No auth.
```

Static, offline-capable (after first font load) client-only app. All logic runs in `<script>` at the end of `index.html`.

## Tech Stack
- **Frontend:** HTML5, CSS3 (custom properties, grid, flex, keyframes), Vanilla JS ES6 — no framework, no JS dependencies
- **Fonts:** Google Fonts `Baloo+2` + `Fredoka` with `fonts.gstatic.com` preconnect and system-ui fallback
- **Storage:** `localStorage` key `sibi_hist` (history only; ideas are emailed, never stored)
- **Share:** hand-drawn `<canvas>` PNG download (`hisab-kitab.png`) + `navigator.clipboard.writeText` list-format message
- **Email:** FormSubmit AJAX to owner inbox (needs hosting + one-time activation; `mailto:` fallback for `file://`)
- **Formatting:** `Number.toLocaleString('en-IN')` for ₹
- **Hosting:** any static host or `file://` — no build step (auto-send email needs `https://`, not `file://`)
- **Monitoring/Analytics/Auth/Payments:** None

## Project Structure
```
/Should I Buy It/
  index.html        # Entire app: <style>, header.topbar, .hero, main (2 cards), footer, 2 modals, <script>
  docs/             # Project documentation (this folder)
    PRD.md
    ARCHITECTURE.md (this file)
    AGENTS.md
    DESIGN_SYSTEM.md
    TRD.md
    APP_FLOW.md
    IMPLEMENTATION_PLAN.md
    TESTING.md
    SECURITY.md
    CODE_STYLE.md
    DATABASE.md
    API.md
```

No `/app`, `/components`, `/lib`, `/services`, `/types`, `/public` — do not invent them unless project migrates to a framework.

## Data Flow
1. User fills placeholder-hinted inputs or taps a preset chip (fill + `calculate(true)` + scroll)
2. `calculate(saveHistory)` validates price (empty/NaN/≤0 → alert, abort), clamps all numbers
3. Computes `hourlyRate`, `effectiveRate`, `hours`, `days`, `months` (see TRD.md)
4. Calls `verdict()` and `equivalents(price, hours, days, savings)` (24 rotating advice lines)
5. Updates DOM: `#rProduct`, `#rHours`, `#rDays`, `#rVerdict`, `#sHours`, `#sDays`, `#sRate`, `#salaryBar`, `#salaryPctTxt`, `#rEquiv`
6. Snapshots `lastHisab` for share; writes history only when `saveHistory` is true (reload/preset-↩ use `false`)
7. Binds `#waBtn` (PNG download) / `#copyBtn` (list text)

Ring rendering builds 20 `.item` divs on a circle radius `stage.clientWidth/2 - 46`, re-laid-out on resize (150ms debounce), hidden from assistive tech.

## Database & Storage
No server DB. See `DATABASE.md`. Only `localStorage.sibi_hist: Array<{product:string, price:number, hours:number}>`.

## External Services
| Service | Purpose | Usage |
|---------|---------|-------|
| Google Fonts | Baloo 2 + Fredoka | `<link>` stylesheet + `gstatic` preconnect; offline → system-ui fallback |
| FormSubmit | Idea emails to owner | `POST https://formsubmit.co/ajax/<owner>` on Send only; `_template: table`, 30s throttle; `mailto:` fallback |
| Clipboard API | Copy list message | `navigator.clipboard.writeText`; insecure-context fallback → `alert` |

No Clerk, Stripe, Resend, PostHog, Sentry, Supabase, WhatsApp integration. Do not add without updating PRD + TRD.

## Deployment
- Copy `index.html` to any static host (Vercel, Netlify, GitHub Pages) or open locally via `file://`.
- No `npm install`, no `npm run build`, no env vars.
- For working idea auto-send: must be served over `https://`, then submit once and click FormSubmit's activation email.
- Footer: creator credit + GitHub/LinkedIn icons + `© 2026` line.

## Scalability Notes
- No scaling concern: fully static, O(1) calc, O(5) history render, canvas drawn on demand.
- If users demand cloud sync / multi-device history / analytics: introduce backend + DB, then update ARCHITECTURE.md, DATABASE.md, API.md, SECURITY.md together.
- Do not add caching layers, queues, background jobs for current scope.

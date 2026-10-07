# Architecture — Should I Buy It? Indian Edition

## System Overview
```
User
 ↓
index.html (HTML + CSS + JS, single file)
 ↓
Browser APIs: localStorage (sibi_hist) | Clipboard API | wa.me share link
External CDN: Google Fonts (Baloo 2 + Fredoka)
No backend. No database server. No auth.
```

This is a static, offline-capable (after first font load) client-only app. All logic runs in `<script>` at end of `index.html:316-470`.

## Tech Stack
- **Frontend:** HTML5, CSS3 (custom properties, grid, flex, keyframes), Vanilla JS ES6 — no framework
- **Fonts:** Google Fonts `Baloo+2` 500/600/700/800 + `Fredoka` 400/500/600/700 via `<link>` in `index.html:8`
- **Storage:** `localStorage` key `sibi_hist`
- **Share:** `https://wa.me/?text=<encodeURIComponent>` + `navigator.clipboard.writeText`
- **Formatting:** `Number.toLocaleString('en-IN')` for ₹
- **Hosting:** Any static host or `file://` — no build step
- **Monitoring/Analytics/Auth/Payments/Email:** None

## Project Structure
```
/Should I Buy It/
  index.html        # Entire app: <style> 9-191, body hero+main+footer 193-314, <script> 316-470
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
1. User types `#product`, `#price`, salary fields or taps preset chip (`index.html:365-370`)
2. `calculate()` (`index.html:398-449`) reads inputs with `Number()` + clamping (`Math.max/min`)
3. Computes `hourlyRate`, `effectiveRate`, `hours`, `days`, `months` (see TRD.md)
4. Calls `verdict()` (`index.html:377-385`) and `equivalents()` (`index.html:387-396`)
5. Updates DOM: `#rProduct`, `#rHours`, `#rDays`, `#rVerdict`, `#sHours`, `#sDays`, `#sRate`, `#salaryBar`, `#salaryPctTxt`, `#rEquiv`
6. Pushes `{product, price, hours}` to `sibi_hist`, slices to 5, calls `renderHist()` (`index.html:451-461`)
7. Binds `#waBtn` / `#copyBtn` with share text

Ring rendering (`index.html:333-349`) runs once on load: builds 20 `.item` divs positioned on circle radius `stage.clientWidth/2 - 46`.

## Database & Storage
No server DB. See `DATABASE.md`. Only `localStorage.sibi_hist: Array<{product:string, price:number, hours:number}>`.

## External Services
| Service | Purpose | Usage |
|---------|---------|-------|
| Google Fonts | Baloo 2 + Fredoka | `<link>` stylesheet, client-side only, graceful fallback to `system-ui,sans-serif` if offline |
| wa.me | WhatsApp share | `window.open('https://wa.me/?text='+encodeURIComponent(txt),'_blank')` — not an API key integration |

No Clerk, Stripe, Resend, PostHog, Sentry, Supabase. Do not add without updating PRD + TRD.

## Deployment
- Copy `index.html` to any static host (Vercel, Netlify, GitHub Pages) or open locally.
- No `npm install`, no `npm run build`, no env vars.
- Footer: `© 2026 Should I Buy It? — Made for India 🇮🇳`

## Scalability Notes
- No scaling concern: fully static, O(1) calc, O(5) history render.
- If users demand cloud sync / multi-device history / analytics: introduce backend + DB, then update ARCHITECTURE.md, DATABASE.md, API.md, SECURITY.md together.
- Do not add caching layers, queues, background jobs for current scope.

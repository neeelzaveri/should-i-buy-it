# Technical Requirements Document — Should I Buy It? Indian Edition

## 1. Project Overview
Build a single-file static web app that converts any ₹ price into work-hours/days/months using Indian salary defaults, with Hinglish verdicts, desi equivalents, local history, and WhatsApp share. No backend.

## 2. Technical Goals
- Instant calc (<50ms) on mobile + desktop, works from `file://`
- 100% client-side; no user data leaves browser except explicit WhatsApp share click
- Never overwrite user inputs on calc; history reload refills inputs then recalculates
- Single `index.html`, zero build, <200KB + fonts
- Responsive from 320px up, no horizontal overflow

## 3. Proposed Tech Stack (actual, not aspirational)
- Frontend: HTML5 + CSS3 + Vanilla JS ES6 (single file)
- Styling: hand-written CSS custom properties, no Tailwind
- Database: `localStorage` only (see DATABASE.md) — no PostgreSQL/Supabase
- Authentication: None
- AI: None
- Hosting: any static host / local file
- Analytics: None; Error monitoring: None (console only)
- If stack changes, update this file + ARCHITECTURE.md together

## 4. Functional Requirements
### Inputs
- `#product` text, `#price` number min 1 (default iPhone 16 / 79900)
- Mode toggle `modeMonthly` / `modeHourly` (`mode` var, default `monthly`)
- Monthly: `#salary` (default 35000), `#hrsPerDay` (9, 1–16), `#daysPerMonth` (26, 1–31), `#savePct` (30, clamp 5–100)
- Hourly: `#hourly` (150), `#savePct2` (100, clamp 5–100)

### Calculation (`calculate()` in `index.html:398-449`)
```
monthly: hourlyRate = salary / (daysPerMonth * hrsPerDay)
hourly:  hourlyRate = hourly input; salary_estimate = hourly*9*26; hrsPerDay = 9
effectiveRate = hourlyRate * (savePct/100)
hours = price / effectiveRate
days = hours / hrsPerDay
months = price / (salary*(savePct/100))  // monthly mode; hourly uses days/26 fallback
salaryBarPct = min(100, price/salary*100)
```
- Format ₹ with `fmtIN = '₹' + Number(Math.round(n)).toLocaleString('en-IN')`
- Hours display: `>=1000 ? round + locale : toFixed(1)`; days similar at >=100
- Empty price → `alert('Price daalo yaar!')` and abort

### Verdict (`verdict(hours,days,price,salary)`)
- `<2h`: 🟢 LE LE LE! Full paisa-vasool
- `<8h`: 🟢 Go for it, boss!
- `<3 days`: 🟡 Socho, phir lo (30-day rule)
- `price/salary <1`: 🟡 Within one month salary, cash not EMI
- `ratio <3`: 🟠 X months salary, SIP mention
- else: 🔴 RUK JAO! X months salary + emergency fund nudge

### Equivalents (`equivalents(price)`)
Divide price by: chai 15, vada pav 25, auto 120, movie 500, rent 500/day. Render: "Isme mil sakte the: X chai OR Y vada pav OR Z auto rides. Soch lo — experience ya cheez? 👀"

### History + Share
- `sibi_hist`: unshift `{product,price,hours}`, slice 0–5, JSON in localStorage, `renderHist()` builds `.hist-item` + ↩ load button
- Share text:
```
🤔 Should I Buy It?
{product} — {₹price}
⏱️ {hours} ghante kaam ({days} days)
💰 Meri rate: {₹/hr}/hr
{🔴 Mehenga / 🟢 Affordable}
— via Should I Buy It? India
```
- WhatsApp: `wa.me/?text=encodeURIComponent(txt)`; Copy: `clipboard.writeText` → `Copied ✅` 1.5s, catch → `alert(txt)`

## 5. Non-Functional Requirements
- Responsive 320px+, breakpoints 860px/480px; hero scales `min(560px,92vw)`
- Visible empty vs result states; bar animates 0.8s
- No API keys client-side (none exist); validate numbers via `Math.max/min`
- Accessible labels, keyboard Enter calc, visible focus (orange ring)
- Fonts degrade to system-ui offline

## 6. Integrations
- Google Fonts CDN only (Baloo 2 + Fredoka). Failure → system font, app still works.
- wa.me share link (not authenticated API). No webhooks.
- No AI, PostHog, Sentry, Stripe — do not document as if present.

## 7. Constraints
- Must stay single-file static; no secrets/env vars; no destructive actions (only localStorage overwrite of own history)
- Salary/price are estimates, not financial advice — footer + verdict copy reflect playful tone
- Clipboard needs secure context (https/localhost/file in most browsers); must keep alert fallback

## 8. Definition of Done
- Open `index.html` → auto-calc shows iPhone example with no console errors
- Preset click fills + calculates + scrolls; mode toggle swaps boxes; Enter key calculates
- Hours math spot-checked (e.g., 35000/(26*9)=₹149.57/hr → 79900 at 30% ≈ 1780 hrs — verify against code, not guess)
- History persists after refresh, max 5; WhatsApp + Copy work
- Mobile 360px + desktop 1100px tested, no overflow; docs updated if formulas change

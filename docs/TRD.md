# Technical Requirements Document — Should I Buy It? Indian Edition

## 1. Project Overview
Build a single-file static web app that converts any ₹ price into work-hours/days/months using Indian salary hints, with Hinglish verdicts, desi equivalents, local history, PNG result saving, and a private email idea box. No backend.

## 2. Technical Goals
- Instant calc (<50ms) on mobile + desktop, works from `file://`
- 100% client-side; nothing leaves the browser except explicit Copy text, PNG download, or idea Send
- Never overwrite user inputs on calc; history reload refills inputs then recalculates without duplicating
- Single `index.html`, zero build, zero JS dependencies, <200KB + fonts
- Responsive from 320px up, no horizontal overflow

## 3. Proposed Tech Stack (actual, not aspirational)
- Frontend: HTML5 + CSS3 + Vanilla JS ES6 (single file)
- Styling: hand-written CSS custom properties, no Tailwind
- Database: `localStorage` only (see DATABASE.md) — no PostgreSQL/Supabase
- Authentication: None
- AI: None
- Email delivery: FormSubmit AJAX (third-party form endpoint, no key)
- Hosting: any static host / local file
- Analytics: None; Error monitoring: None (no `console.*` in production code)
- If stack changes, update this file + ARCHITECTURE.md together

## 4. Functional Requirements
### Inputs (all placeholder-hinted, empty by default; JS fallbacks in brackets)
- `#product` text (fallback `'Ye product'`)
- `#price` number — empty/NaN/≤0 → `alert('Price daalo yaar!')`, abort, no history
- Mode toggle `modeMonthly` / `modeHourly` (`mode` var, default `monthly`)
- Monthly: `#salary` (35000), `#hrsPerDay` (9, clamp 1–24), `#daysPerMonth` (26, clamp 1–31), `#savePct` (30, clamp 5–100)
- Hourly: `#hourly` (150), `#hourlyHrs` (9, clamp 1–24), `#savePct2` (100, clamp 5–100)

### Calculation (`calculate(saveHistory=true)`)
```
monthly: hourlyRate = salary / (daysPerMonth * hrsPerDay)
hourly:  hourlyRate = hourly input; hrsPerDay = hourlyHrs; salary_estimate = hourly * hourlyHrs * 26
effectiveRate = hourlyRate * (savePct/100)
hours = price / effectiveRate
days = hours / hrsPerDay
months = price / (salary*(savePct/100))
salaryBarPct = price/salary*100 (capped 100; CSS min-width 3px keeps sliver visible)
```
- Format ₹ with `fmtIN = '₹' + Number(Math.round(n)).toLocaleString('en-IN')`
- Hours display: `>=1000 ? round + locale : toFixed(1)`; days similar at >=100
- `saveHistory=false` on page-load paths, preset-↩ reloads, EMI-less live recodes and modal-adjacent recalcs; `true` on Calculate button, presets, Enter
- Snapshot `lastHisab` (inputs + verdict/equiv HTML + pct text) for share builders

### Verdict (`verdict(hours,days,price,salary)`)
- `<2h`: 🟢 LE LE! Full paisa-vasool
- `<8h`: 🟢 Go for it, boss!
- `<3 days`: 🟡 Socho, phir lo (30-day rule)
- `price/salary <1`: 🟡 Within one month salary, cash not EMI
- `ratio <3`: 🟠 X months salary, SIP mention
- else: 🔴 RUK JAO! X months salary + emergency fund nudge

### Equivalents (`equivalents(price, hours, days, salary)`)
Divide price by: chai 15, vada pav 25, auto 120, movie 500, rent 500/day. Closing advice reacts to the same 6 tiers with 4 rotating lines each (24 total, `Math.random` pick per calc).

### History
- `sibi_hist`: unshift `{product,price,hours}`, slice 0–5, JSON in localStorage, `renderHist()` builds rows via `textContent` only (XSS-safe) + ↩ button with `aria-label`

### Save Hisab PNG (`drawHisab()` → `hisabPng()`)
- Hand-drawn 2x canvas, 680px design width: title → subtitle → black hours card → dashed verdict → 3 stats → salary-% bar → equivalents → 3-line site/creator footer
- Long product names truncate with `…` (measured, never clipped); heights computed from wrapped-line counts before sizing canvas
- Download as `hisab-kitab.png` via blob URL; `Saved ✅` / `Try again 🔁` states

### Copy list message (`buildHisabText()`)
- Same section order as the PNG, plain-text lines (`shareText()` strips the verdict/equiv HTML, which contains numbers + static copy only)

### Feature ideas (`initFeats`)
- Required name (`Your name * — we'd love to credit your idea`), idea ≤800 chars, HTML-escaped into a cozy HTML body
- `POST https://formsubmit.co/ajax/<owner>` with `_subject`, `_template: table`, `_captcha: false`; checks `data.success==='true'` (HTTP 200 lies on `file://`)
- 30s throttle (`lastFeatSentAt`); button disabled until settle (`finally`), label restored after
- `mailto:` fallback with plain-text prefill when auto-send fails; old `sibi_features` key removed on load

## 5. Non-Functional Requirements
- Responsive 320px+, breakpoints 860px/480px/560px; hero scales `min(560px,92vw)` with overlap-safe padding
- Visible empty (badge row) vs result states; bar animates 0.8s (off under reduced-motion)
- No API keys client-side (owner email is a routing address, not a secret); validate numbers via clamping + price guard
- Accessible labels incl. `for`/`required`, keyboard Enter calc, orange `:focus-visible`, modal focus traps, `aria-hidden` decorative ring
- Fonts degrade to system-ui offline

## 6. Integrations
- Google Fonts CDN (Baloo 2 + Fredoka + gstatic preconnect). Failure → system font, app still works.
- FormSubmit (idea delivery only). Requires hosting + activation; `file://` gets honest fallback message.
- Clipboard API (copy). No WhatsApp integration (Save Hisab replaced it).
- No AI, PostHog, Sentry, Stripe — do not document as if present.

## 7. Constraints
- Must stay single-file static; no secrets/env vars; no destructive actions (only localStorage overwrite of own history)
- Salary/price are estimates, not financial advice — disclaimer modal + verdict copy reflect playful tone
- Clipboard/PNG download need secure context for best behavior; copy keeps `alert` fallback

## 8. Definition of Done
- Fresh open shows badge empty state (no auto-calc, no alert), no console errors (there is no logging)
- Preset click fills + calculates + saves + scrolls; mode toggle swaps boxes; Enter calculates
- Hours math spot-checked: 35000/(26*9)=₹149.57/hr → 69900 at 30% ≈ 1558 hrs ≈ 173 days ≈ 6.7 months — verify against code, not guess
- History persists after refresh, max 5, reload never duplicates; Save PNG downloads; Copy matches card order
- Idea Send works hosted (activation done); `file://` shows fallback guidance
- Mobile 360px + desktop 1100px tested, no overflow; docs updated if formulas change

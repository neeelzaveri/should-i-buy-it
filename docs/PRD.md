# Product Requirements Document — Should I Buy It? Indian Edition

## Product Overview
Product: Should I Buy It? Indian Edition
One-liner: Enter any product price in ₹, enter your salary, instantly see how many work-hours it costs.
Vision: Stop impulse buying with a fun, desi, Hinglish "hisab-kitab" that converts ₹ into time.
Current implementation: Single static file `index.html` (~880 lines). No backend, no signup. Open file → fill hints → calculate → save/copy.

## Problem
- Indian shoppers (students, salaried, gig workers) see prices in ₹ but don't feel the time-cost.
- EMI + impulse buying hides true affordability.
- Existing calculators are boring, dollar-centric, require signup.

## Goal
Help a user decide in <30 seconds: "Is this paisa-vasool or RUK JAO?" with hours, days, months-savings, and desi equivalents.

## Target Users
- Salaried employees (avg India ₹25–40k/month, mostly 8–10 hrs/day, 26 days/month hints)
- Students / first-job seekers evaluating phones, sneakers, laptops
- Gig workers (Zomato ~₹80-100/hr, fresher jobs ~₹100-150/hr, freelance ₹300+/hr) using hourly mode
- Hinglish-speaking mobile-first users who save the result as a PNG image

## Core Features (built)
1. **Product + Price input** (`#product`, `#price`): empty with hints (`e.g. iPhone 18`, `e.g. Rs 69900`); empty/≤0 price alerts and aborts
2. **Indian presets** (`#presets`): Cutting Chai ₹15, Vada Pav ₹25, Metro Recharge ₹500, Sneakers ₹2999, Boat Headphones ₹1999, iPhone 18 Pro ₹164900, Royal Enfield ₹193000, MacBook Air M4 ₹99900, Gold 10g (22K) ₹140000, Goa Trip ₹30000 — one click fills + calculates + saves history
3. **Salary modes**:
   - Monthly: `#salary`, `#hrsPerDay` (1–24), `#daysPerMonth` (1–31), `#savePct` (5–100) — all placeholder hints with JS fallbacks
   - Hourly: `#hourly`, `#hourlyHrs` (1–24), `#savePct2` — salary estimated as hourly × hours × 26
4. **Calculate engine** (`calculate(saveHistory)`): hours, work-days, months-savings, effective ₹/hr; history written only on explicit user action
5. **Verdict engine** (`verdict()`): 6 tiers 🟢/🟡/🟠/🔴 with Hinglish copy ("LE LE!" green tier)
6. **Salary % bar** (`#salaryBar`): price as % of monthly salary, honest width + 3px CSS minimum
7. **Desi equivalents** (`equivalents()`): chai / vada pav / auto rides / movie / rent-days + 24 rotating advice lines (4 per tier) reacting to the numbers
8. **History** (`sibi_hist` in localStorage, max 5, `#histList` with XSS-safe rows + ↩ reload that never duplicates)
9. **Save Hisab PNG** (`#waBtn`): hand-drawn canvas image of the result card (title → hours card → verdict → stats → salary bar → equivalents → site + creator footer), downloaded as `hisab-kitab.png`, zero dependencies
10. **Copy list message** (`#copyBtn`): section-by-section plain-text mirror of the card image
11. **Hero collage ring**: rotating emoji circle (20 items incl. 📱), resize-aware layout, `aria-hidden`, center copy "Everything and Anything / IT'S TIME TO HISAB!"
12. **Feature requests** (`#featName` required — "we'd love to credit your idea", `#featIdea`): modal from header 💡 tile; auto-send via FormSubmit as cozy HTML email to owner (30s throttle, `mailto:` fallback for `file://` use); nothing stored or shown publicly
13. **Disclaimer modal**: 6 bullets (💡🔍⏰📊🔒💛), opened from an inline link in the input card's bottom note; focus-trapped like all modals
14. **Creator footer**: "Created by Neel Zaveri ✨" + GitHub + LinkedIn SVG icons (new tab)

## User Flows
- **Main**: Land → see hero → read hints → enter product/price → pick salary mode → Calculate → read verdict/stats/equivalents → Save Hisab PNG / Copy → check history
- **First open**: empty state (badge row + "Abhi kuch calculate nahi kiya") — no auto-calc, no alert
- **Preset shortcut**: Tap chip → auto-fill → calculate + save → scroll to `main`
- **History reload**: Tap ↩ → refill product/price → recalculate without duplicating
- **Mode switch**: Toggle Monthly/Hourly → show/hide `#monthlyBox` / `#hourlyBox`
- **Idea flow**: 💡 tile → modal → Send → status message (delivered / fallback / throttle)
- **Disclaimer flow**: inline link → bulleted modal → ✕ / overlay / Esc

## Requirements
### Functional
- Calculation 100% client-side; network only for Google Fonts + FormSubmit (on explicit Send)
- Enter key on all 9 calc inputs triggers calculate (with history)
- Empty/non-numeric/≤0 price → alert, no result change, no history write
- History persists across refresh via localStorage, never exceeds 5
- Copy text mirrors card sections in order; PNG covers card top → above action buttons

### UX
- Hinglish tone: "Kya lena hai?", "Kitne Ghante?", "PAISA • VASOOL? 🤔"
- Neo-brutalist cards: 3px #111 border, 8px 8px 0 #111 shadow, 22px radius
- Empty state: 5 tilted emoji badges + "Abhi kuch calculate nahi kiya"
- Mobile-first: 2-col → 1-col under 860px, row2 → 1-col under 480px, hero/ring adjustments under 560px

### Performance
- Single file, no build, no JS dependencies; only Google Fonts CDN
- Calc <50ms, bar animation 0.8s ease, ring rotation 90s linear infinite (disabled under reduced-motion)
- Must work from `file://` double-click (calc/history/copy/PNG all local; only email auto-send needs hosting)

### Platform
- Modern Chrome/Edge/Firefox/Safari desktop + mobile
- 320px width minimum, no horizontal overflow
- Clipboard/share PNG need secure context; copy falls back to alert text

## Success Metrics
- Calculation completion rate (land → calc click)
- Preset click rate
- PNG save / copy rate
- Idea emails received
- Return usage via history presence
- Zero backend cost, <200KB page weight

## Out of Scope (V1)
- Accounts, login, cloud sync, backend DB, payments, EMI/SIP calculators
- Custom domains per user, native apps, multi-currency (₹ only)
- Server analytics, ads, price tracking

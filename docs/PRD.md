# Product Requirements Document — Should I Buy It? Indian Edition

## Product Overview
Product: Should I Buy It? Indian Edition
One-liner: Enter any product price in ₹, enter your salary, instantly see how many work-hours it costs.
Vision: Stop impulse buying with a fun, desi, Hinglish "hisab-kitab" that converts ₹ into time.
Current implementation: Single static file `index.html` (472 lines). No backend, no signup. Open file → calculate → share.

## Problem
- Indian shoppers (students, salaried, gig workers) see prices in ₹ but don't feel the time-cost.
- EMI + impulse buying hides true affordability.
- Existing calculators are boring, dollar-centric, require signup.

## Goal
Help a user decide in <30 seconds: "Is this paisa-vasool or RUK JAO?" with hours, days, months-salary, and desi equivalents.

## Target Users
- Salaried employees (₹25–40k/month typical, 8–10 hrs/day, 26 days/month defaults)
- Students / first-job seekers evaluating phones, sneakers, laptops
- Gig workers (Zomato ~₹60/hr, fresher ~₹150–250/hr) using hourly mode
- Hinglish-speaking mobile-first users who share on WhatsApp

## Core Features (V1 — already built)
1. **Product + Price input** (`#product`, `#price`): text + number, defaults `iPhone 16 / 79900`
2. **Indian presets** (`#presets`): Cutting Chai ₹15, Vada Pav ₹25, Metro ₹500, Sneakers ₹2999, Boat ₹1999, iPhone 16 ₹79900, Royal Enfield ₹193000, MacBook Air ₹114900, Gold 10g ₹72000, Goa Trip ₹30000 — one click fills + calculates
3. **Salary modes**:
   - Monthly: `#salary`, `#hrsPerDay`, `#daysPerMonth`, `#savePct`
   - Hourly: `#hourly`, `#savePct2`
4. **Calculate engine** (`calculate()`): hours, work-days, months-savings, effective ₹/hr
5. **Verdict engine** (`verdict()`): 5 tiers 🟢/🟡/🟠/🔴 with Hinglish copy
6. **Salary % bar** (`#salaryBar`): price as % of monthly salary
7. **Desi equivalents** (`equivalents()`): chai / vada pav / auto rides / movie / rent-days
8. **History** (`sibi_hist` in localStorage, max 5, `#histList` with reload button)
9. **Share**: WhatsApp `wa.me/?text=` + Clipboard `navigator.clipboard.writeText` with fallback alert
10. **Hero collage ring**: rotating emoji circle (20 items), center copy "Everything and Anything / IT'S TIME TO HISAB!"

## User Flows
- **Main**: Land → see hero → enter product/price → pick salary mode → Calculate → read verdict/stats/equivalents → WhatsApp/Copy → check history
- **Preset shortcut**: Tap chip → auto-fill → auto-calculate → scroll to `main`
- **History reload**: Tap ↩ on history item → refill product/price → recalculate
- **Mode switch**: Toggle Monthly/Hourly → show/hide `#monthlyBox` / `#hourlyBox`

## Requirements
### Functional
- Calculation must run 100% client-side, no network except Google Fonts
- Enter key on any input triggers calculate
- Auto-calculate on load for demo
- History persists across refresh via localStorage, never exceeds 5
- Share text format: product, formatted ₹ (en-IN), hours, days, rate, affordable flag

### UX
- Hinglish tone: "Kya lena hai?", "Kitne Ghante?", "PAISA • VASOOL? 🤔"
- Neo-brutalist cards: 3px #111 border, 8px 8px 0 #111 shadow, 22px radius
- Empty state: `🛵☕📱👟⏰ / Abhi kuch calculate nahi kiya` until first calc
- Mobile-first: 2-col → 1-col under 860px, row2 → 1-col under 480px

### Performance
- Single file, no build, no dependencies except Google Fonts
- Calc <50ms, bar animation 0.8s ease, ring rotation 90s linear infinite
- Must work from `file://` double-click with no server

### Platform
- Modern Chrome/Edge/Firefox/Safari desktop + mobile
- 320px width minimum, no horizontal overflow
- Clipboard requires secure context; fallback to alert with text

## Success Metrics
- Calculation completion rate (land → calc click)
- Preset click rate
- WhatsApp share / copy rate
- Return usage via history presence
- Zero backend cost, <200KB page weight

## Out of Scope (V1)
- Accounts, login, cloud sync, backend DB, payments, EMI calculator
- Custom domains per user, native apps, multi-currency (₹ only)
- Server analytics, ads, price tracking

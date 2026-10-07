# App Flow — Should I Buy It? Indian Edition

## 1. Primary Journey
```
Landing (hero ring + "IT'S TIME TO HISAB!")
→ Read left card "Kya lena hai?" (all fields show hint placeholders)
→ Enter Product + Price (or tap preset chip)
→ Choose Salary mode (Monthly / Hourly)
→ Fill salary fields (or trust documented fallbacks)
→ [Enter] or Click "Calculate ⏱️ Kitne Ghante?"
→ Read right card "Hisab-kitab" (big hours, verdict, stats, bar, equivalents)
→ Save Hisab PNG / Copy list message OR check Recent hisab
→ Reload history item ↩ → recalculates without duplicating
```

## 2. Screen Details
Single page, two cards (`main` grid) + two modals + footer. No routing.

### Hero
- Purpose: brand + delight, explain concept in 3s
- Content: rotating emoji ring (20 items incl. 📱, resize-aware, `aria-hidden`), center `Everything and Anything / ⏰ IT'S TIME TO HISAB! / product daalo, price daalo...`
- Overlap-safe: 98px desktop / 80px mobile padding, smaller badges + type under 560px
- Actions: none (decorative); preset tap scrolls to `main`

### Header
- Left: logo bubbles. Right (`.top-actions`): `🇮🇳 Indian Edition • ₹` pill + pink `💡 Idea?` tile
- Idea tile opens the feature modal (focus → textarea); wraps right-aligned under 560px

### Left Card — Input
- Purpose: collect price + earnings
- Inputs: `#product`, `#price` (placeholders, no defaults), `#presets` chips, `#modeMonthly/#modeHourly` seg, `#monthlyBox` (`salary, hrsPerDay, daysPerMonth, savePct`) / `#hourlyBox` (`hourly, hourlyHrs, savePct2`)
- Bottom note merges trust + disclaimer entry: `No signup • 100% free • data stays in your browser • Disclaimer 🔒` (inline-link opens bulleted modal)
- Actions: preset chip (fill + calc + save + smooth scroll), mode toggle (show/hide boxes), `#calcBtn`
- Errors: empty/NaN/≤0 price → `alert('Price daalo yaar!')`, nothing else changes; numbers clamped (hrs 1–24, days 1–31, save 5–100)
- Rules: Enter key in any of 9 inputs triggers calc (with history)

### Right Card — Result
- Purpose: reveal time-cost + nudge decision
- Empty (`#emptyState`): 5 tilted badge circles + `Abhi kuch calculate nahi kiya / Left side me price daalo...`
- Result (`#resultBox`): `#rProduct` (wraps), `#rHours` (yellow on black), `#rDays`, `#rVerdict` (color tier), stats (`sHours/sDays/sRate`), `#salaryBar` + `#salaryPctTxt`, `#rEquiv` (comparisons + rotating tier advice), actions (`waBtn` Save Hisab / `copyBtn` Copy), `.history > #histList`
- Actions: Save Hisab → `Making image… ⏳` → download `hisab-kitab.png` → `Saved ✅`; Copy → list text → `Copied ✅`; history ↩ (refill + calc, no duplicate)
- Failure: clipboard denied → alert with text; localStorage blocked → try/catch silent, calc still shows; PNG fail → `Try again 🔁`

### Feature Modal (`#featModal`)
1. Required name + idea; Send validates both
2. Throttle check (30s) → POST FormSubmit → success clears + confirms
3. Failure → `mailto:` fallback with prefill + guidance; button re-enables on settle
4. Close via ✕ / overlay / Esc; focus trapped while open

### Disclaimer Modal (`#discModal`)
- 6 bullets (💡🔍⏰📊🔒💛); opened from input-card link; same close/focus behavior

### Generate (calc is instant, no loading spinner)
1. Validates price (guard, not clamp-then-alert)
2. Computes rates/hours (see TRD.md)
3. Updates DOM synchronously + animates bar via `requestAnimationFrame`
4. Snapshots `lastHisab`; saves history only when asked; binds share buttons

## 3. Secondary Flows
### Preset Shortcut
Tap `Cutting Chai ☕ • ₹15` → fill → `calculate(true)` → scroll to `main`

### Mode Switch
Monthly ↔ Hourly: toggles `.active` class + `display:block/none` on boxes. Values preserved per box. Hourly salary estimate follows `hourlyHrs`.

### History Reload
Tap ↩ → set `#product/#price` from entry → `calculate(false)` (recomputes with current salary, not stored hours)

### Keyboard
Enter in any of 9 calc inputs → `calculate(true)`; Esc closes modals; Tab trapped inside open modals

## 4. Important States
- Empty (fresh open shows badges — no auto-calc by design)
- Calculated (verdict 🟢/🟡/🟠/🔴 + rotating advice line)
- Validation alert (price missing/invalid)
- Sending… / Sent / fallback / throttle-hold (idea form)
- Copied / Saved (1.5–2.2s confirmations)
- History empty (`Koi history nahi — pehla hisab karo!`) vs populated (≤5)
- Responsive: 2-col → 1-col; ring/badges scale; no separate mobile screens
- Offline: calc/history/PNG all local; fonts fallback; email auto-send unavailable (`mailto:` path)

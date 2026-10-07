# App Flow — Should I Buy It? Indian Edition

## 1. Primary Journey
```
Landing (hero ring + "IT'S TIME TO HISAB!")
→ Read left card "Kya lena hai?"
→ Enter Product + Price (or tap preset chip)
→ Choose Salary mode (Monthly / Hourly)
→ Fill salary fields
→ [Enter] or Click "Calculate ⏱️ Kitne Ghante?"
→ Read right card "Hisab-kitab" (big hours, verdict, stats, bar, equivalents)
→ Share on WhatsApp / Copy OR check Recent hisab
→ Reload history item ↩ → recalculates
```

## 2. Screen Details
Single page, two cards (`main` grid). No routing.

### Hero
- Purpose: brand + delight, explain concept in 3s
- Content: rotating emoji ring (20 items, `index.html:321-332`), center `Everything and Anything / ⏰ IT'S TIME TO HISAB! / product daalo, price daalo...`
- Actions: none (decorative); scrolls to `main` on preset click

### Left Card — Input
- Purpose: collect price + earnings
- Inputs: `#product`, `#price`, `#presets` chips, `#modeMonthly/#modeHourly` seg, `#monthlyBox` (`salary, hrsPerDay, daysPerMonth, savePct`) / `#hourlyBox` (`hourly, savePct2`)
- Actions: preset chip (fill + calc + smooth scroll), mode toggle (show/hide boxes), `#calcBtn`
- Errors: empty/0 price → `alert('Price daalo yaar!')`; numbers clamped (`Math.max/min`)
- Rules: defaults prefilled (iPhone 16, 79900, 35000...); Enter key anywhere triggers calc

### Right Card — Result
- Purpose: reveal time-cost + nudge decision
- Empty (`#emptyState`): `🛵☕📱👟⏰ / Abhi kuch calculate nahi kiya / Left side me price daalo...`
- Result (`#resultBox`): `#rProduct`, `#rHours` (yellow on black), `#rDays`, `#rVerdict` (color tier), stats (`sHours/sDays/sRate`), `#salaryBar` + `#salaryPctTxt`, `#rEquiv`, actions (`waBtn/copyBtn`), `.history > #histList`
- Actions: WhatsApp (new tab), Copy (→ Copied ✅), history ↩ (refill + calc)
- Failure: clipboard denied → alert with text; localStorage full/disabled → try/catch silent, calc still shows

### Generate (calc is instant, no loading spinner)
1. Validates price >0
2. Computes rates/hours (see TRD.md)
3. Updates DOM synchronously + animates bar via `requestAnimationFrame`
4. Saves history, binds share buttons

## 3. Secondary Flows
### Preset Shortcut
Tap `Cutting Chai ☕ • ₹15` → fill → `calculate()` → scroll to `main`

### Mode Switch
Monthly ↔ Hourly: toggles `.active` class + `display:block/none` on boxes. Values preserved per box.

### History Reload
Tap ↩ → set `#product/#price` from entry → `calculate()` (recomputes with current salary, not stored hours)

### Keyboard
Enter in any of 8 inputs → `calculate()`

## 4. Important States
- Empty (pre-first-calc, or cleared via devtools)
- Calculated (verdict 🟢/🟡/🟠/🔴)
- Validation alert (price missing)
- Copied (button label 1.5s)
- History empty (`Koi history nahi — pehla hisab karo!`) vs populated (≤5)
- Responsive: 2-col → 1-col; ring scales; no separate mobile screens
- Offline: works after load except fonts fallback; wa.me needs internet

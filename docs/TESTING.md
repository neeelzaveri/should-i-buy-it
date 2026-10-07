# Testing Guide — Should I Buy It? Indian Edition

## 1. Testing Goal
Confirm the ₹→hours math is correct, verdicts + advice agree, history/share/PNG/email work, layout holds on mobile, and no data leaves the browser except explicit Copy/PNG/Send. Manual testing only — no runner configured.

## 2. Critical User Journey (release blocker if broken)
```
Open index.html → badge empty state, no alert
→ Fill product + price + salary → Calculate → result card complete
→ Tap iPhone 18 Pro preset → recalculates + saves + scrolls
→ Switch to Hourly 150 × 9h → recalculates
→ Save Hisab → hisab-kitab.png downloads
→ Copy → list text matches card order
→ Refresh → history shows entries, no duplicates
→ 360px mobile → no overflow
```

## 3. Input Tests
- [ ] Empty / 0 / negative / `abc` price → `alert('Price daalo yaar!')`, no result change, no history entry
- [ ] Empty salary fields → documented fallbacks apply (35000/9/26/30, hourly 150/9/100)
- [ ] Very long product name → wraps in card, truncates with … in PNG
- [ ] Product `<img src=x onerror=alert(1)>` → inert text in card/history/PNG/email; executes nowhere
- [ ] hrsPerDay 0 / 99 → clamped 1–24; daysPerMonth clamped 1–31; savePct clamped 5–100
- [ ] Enter key in each of 9 inputs triggers calculate (with history)

## 4. Calculation Tests (recompute by hand, don't trust old numbers)
- [ ] Monthly: salary 35000, 26d, 9h → hourly ≈ 149.57; price 69900 save 30% → hours ≈ 1558h, days ≈ 173, months ≈ 6.7 — verify code matches
- [ ] Hourly: 150/hr × 9h save 100%, price 1500 → 10.0 hrs; changing hourlyHrs changes salary estimate + days
- [ ] Save 30% triples hours vs 100% for same price
- [ ] `fmtIN` → ₹69,900 (en-IN grouping), ₹1,64,900 for iPhone 18 Pro, ₹1,93,000 for Enfield
- [ ] Bar width = price/salary*100 capped 100 (e.g., 69900/35000 → 100%, text ~200%)

## 5. Verdict Tests
- [ ] <2h → 🟢 LE LE
- [ ] 2–8h → 🟢 Go for it
- [ ] 8h–3d → 🟡 Socho phir lo
- [ ] <1 month salary but >3d → 🟡 within salary
- [ ] 1–3 months → 🟠 SIP warning
- [ ] ≥3 months → 🔴 RUK JAO
- [ ] Equivalents closing line tier-matches verdict and varies across recalcs

## 6. Equivalents / Stats Tests
- [ ] 1500 → 100 chai, 60 vada pav, 12 auto rides (floor values)
- [ ] Stats `sHours/sDays/sRate` match big-number
- [ ] Preview (`rDays` text) shows work-days + months/days savings + ₹/hr

## 7. History / Share Tests
- [ ] 6 calculates → only latest 5 kept
- [ ] Refresh persists; ↩ reloads product/price and recalculates with current salary, no duplicate row
- [ ] Reload never adds rows (page load, ↩ both use `saveHistory=false`)
- [ ] Private mode (localStorage blocked) → calc still works, no throw (try/catch)
- [ ] Save Hisab → PNG downloads; contains title → card → verdict → stats → bar → equivalents → 3-line footer
- [ ] Copy → `Copied ✅` 1.5s → revert; section order mirrors card; offline/blocked clipboard → alert fallback

## 8. Idea Form Tests (hosted URL only for auto-send)
- [ ] Empty idea / empty name → alerts, focus moves, nothing sent
- [ ] Rapid double Send → single email (30s throttle message)
- [ ] Slow network → button stays disabled until settle (no duplicates)
- [ ] Success → idea cleared, confirmation shown; nothing stored locally
- [ ] `file://` → honest fallback message + prefilled `mailto:` opens

## 9. Responsive Checks (320, 360, 768, 1100)
- [ ] No horizontal overflow; ring `92vw` centered, never overlaps headline
- [ ] ≤860px single column; ≤480px `row2` single column; ≤560px hero/topbar adjustments
- [ ] Buttons tappable (↩ ≥36px); chips wrap; modals scroll within 90vh
- [ ] `#histList` scrolls at 230px max, thumb visible
- [ ] 200% zoom usable

## 10. Accessibility Checks
- [ ] All inputs have `<label>` (`for` + `required` on name); keyboard reaches chips/seg/CTA/history ↩/modals
- [ ] Focus visible (orange) + trapped inside open modals; Esc/overlay close; focus restored
- [ ] Heading order H1→H2→H3; errors not color-only (verdict has text+emoji)
- [ ] Ring emojis `aria-hidden`; history ↩ has `aria-label`; reduced-motion disables spin/bar
- [ ] CTA/footer contrast passes large-text AA

## 11. Security / Privacy Checks
- [ ] View-source shows no keys, tokens, backend endpoints; only Fonts + FormSubmit URLs
- [ ] `sibi_hist` contains only product/price/hours — inspect Application tab
- [ ] No network calls except fonts + FormSubmit-on-Send (DevTools Network)
- [ ] History rows via `textContent`; `innerHTML` only at guarded number-derived sinks
- [ ] Email HTML escapes visitor input; owner address is routing, not a secret

## 12. Release Blockers
Do not release if: calc math wrong, verdict tier wrong, advice tier mismatched, history loses data or duplicates on refresh, PNG missing sections, copy order wrong, mobile overflow, clipboard+fallback both broken, ideas stored publicly, private data sent anywhere.

## 13. Test Result Format
- Test: / Expected: / Actual: / Device-browser: / Steps: / Screenshot-log: / Severity: / Status:
- Example: Test: Enfield preset / Expected: ~4300h at defaults / Actual: ___ / Device: Moto G Chrome / Status: pass/fail

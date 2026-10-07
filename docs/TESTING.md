# Testing Guide — Should I Buy It? Indian Edition

## 1. Testing Goal
Confirm the ₹→hours math is correct, verdicts nudge sensibly, history/share work, layout holds on mobile, and no data leaves the browser except explicit share. Manual testing only — no runner configured.

## 2. Critical User Journey (release blocker if broken)
```
Open index.html → auto-calc shows iPhone result
→ Change price to 2999 + salary 35000 → Calculate → hours ≈ 67h (?) recompute
→ Tap Royal Enfield preset → recalculates + scrolls
→ Switch to Hourly 150 → recalculates
→ WhatsApp + Copy
→ Refresh → history shows entries
→ 360px mobile → no overflow
```

## 3. Input Tests
- [ ] Empty price → `alert('Price daalo yaar!')`, no crash
- [ ] Price 0 / negative → clamped to ≥1, calculates
- [ ] Very long product name → wraps, doesn't break `.big-number`
- [ ] Special chars `<script>` in product → rendered as text in `rProduct`/history (no execution; share uses encodeURIComponent)
- [ ] hrsPerDay 0 / 99 → clamped 1–16; daysPerMonth clamped 1–31; savePct clamped 5–100
- [ ] Enter key in each input triggers calculate

## 4. Calculation Tests (recompute by hand, don't trust old numbers)
- [ ] Monthly: salary 35000, 26d, 9h → hourly ≈ 149.57; price 79900 save 30% → hours ≈ 79900/(149.57*0.3) ≈ 1780h, days ≈ 198, months ≈ 7.6 — verify code matches
- [ ] Hourly: 150/hr save 100%, price 1500 → 10.0 hrs
- [ ] Save 30% triples hours vs 100% for same price
- [ ] `fmtIN` → ₹79,900 (en-IN grouping), ₹1,93,000 for Enfield
- [ ] Bar % = price/salary*100 capped 100 (e.g., 79900/35000 → 100% width, text 228%)

## 5. Verdict Tests
- [ ] <2h → 🟢 LE LE LE
- [ ] 2–8h → 🟢 Go for it
- [ ] 8h–3d → 🟡 Socho phir lo
- [ ] <1 month salary but >3d → 🟡 within salary
- [ ] 1–3 months → 🟠 SIP warning
- [ ] ≥3 months → 🔴 RUK JAO

## 6. Equivalents / Stats Tests
- [ ] 1500 → 100 chai, 60 vada pav, 12 auto rides (floor values)
- [ ] Stats `sHours/sDays/sRate` match big-number
- [ ] Preview (`rDays` text) shows work-days + months/days savings + ₹/hr

## 7. History / Share Tests
- [ ] 6 calculates → only latest 5 kept
- [ ] Refresh persists; ↩ reloads product/price and recalculates with current salary
- [ ] Private mode (localStorage blocked) → calc still works, no throw (try/catch)
- [ ] WhatsApp opens `wa.me/?text=` with encoded text in new tab
- [ ] Copy → `Copied ✅` 1.5s → revert; offline/blocked clipboard → alert fallback

## 8. Responsive Checks (360, 768, 1100)
- [ ] No horizontal overflow; ring `92vw` centered
- [ ] ≤860px single column; ≤480px `row2` single column
- [ ] Buttons tappable ≥44px; chips wrap; modal/history scroll works
- [ ] `#histList` scrolls at 230px max, thumb visible

## 9. Accessibility Checks
- [ ] All inputs have `<label>`; keyboard reaches chips/seg/CTA/history ↩
- [ ] Focus ring visible (orange); heading order H1→H2→H3; errors not color-only (verdict has text+emoji)
- [ ] Zoom 200% usable; emojis decorative, info in text

## 10. Security / Privacy Checks
- [ ] View-source shows no keys, tokens, endpoints; only Google Fonts URL
- [ ] `sibi_hist` contains only product/price/hours — inspect Application tab
- [ ] No network calls except fonts + wa.me on click (DevTools Network)
- [ ] `innerHTML` only for app-built strings (verdict/equiv/history); product name with HTML does not execute

## 11. Release Blockers
Do not release if: calc math wrong, verdict tier wrong, history loses data on normal refresh, share text missing ₹/hours, mobile overflow, clipboard+fallback both broken, private data sent anywhere.

## 12. Test Result Format
- Test: / Expected: / Actual: / Device-browser: / Steps: / Screenshot-log: / Severity: / Status:
- Example: Test: Enfield preset / Expected: ~4300h at defaults / Actual: ___ / Device: Moto G Chrome / Status: pass/fail

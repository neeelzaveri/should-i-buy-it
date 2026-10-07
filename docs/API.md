# API.md — Should I Buy It? Indian Edition

## 1. Purpose
No backend REST/GraphQL/tRPC. Frontend-only. This file documents internal JS "API" (functions) + browser/external integrations so agents don't invent `/api/v1/projects`.

## 2. Base Configuration
- Backend base URL: None. App runs from `file://` or any static host.
- Response format: DOM updates + PNG download, not JSON over HTTP.
- Versioning: None. Single `index.html`.

## 3. Authentication
None. No headers, tokens, or `Authorization: Bearer`. Never expose server credentials — none exist. (Owner email in JS is a form-routing address, not a credential.)

## 4. Endpoint Conventions (internal functions, not HTTP)
Use these instead of REST:
```
calculate(saveHistory=true) # validates price, clamps, updates #resultBox + lastHisab; writes history only when true
verdict(hours, days, price, salary) -> string (HTML, numbers + static copy only)
equivalents(price, hours, days, salary) -> string (HTML, 24 rotating advice lines)
renderHist(hist)            # Array<HistoryEntry> => void (XSS-safe: textContent only)
buildHisabText()            # reads lastHisab -> list-format share message
drawHisab()/hisabPng()      # renders card to canvas -> PNG File (truncates long names)
shareText(html)             # strips verdict/equiv HTML to plain text (inputs are number-derived)
fmtIN(n) -> "₹69,900"
buildRing()/layout()        # renders 20 emoji items, re-layouts on resize
trapTab(modal, e)           # keeps Tab focus inside open modals
```
- Nouns for resources N/A; keep function names stable. Don't add `/api/*` routes unless backend approved.

## 5. Example "Endpoint" — calculate()
**Trigger:** click `#calcBtn` or Enter in 9 inputs (saves) or preset tap (saves) or history ↩ / EMI-less recodes (display-only)
**Input (DOM, not JSON body):** `{product:string, price:number, mode:'monthly'|'hourly', salary|hourly, hrsPerDay|hourlyHrs, daysPerMonth, savePct}`
**Success (DOM):**
- `#rProduct: "iPhone 18 Pro • ₹1,64,900"`, `#rHours`, `#rDays`, `#rVerdict`, stats, bar, `#rEquiv`
- `lastHisab` snapshot; history prepended (only when saving); share handlers bound
**Validation error:** `alert('Price daalo yaar!')` on empty/non-numeric/≤0 price, no DOM change, no history write.

Copy message (mirrors card sections in order):
```
⌚ Hisab-kitab
"{product}" ka poora sach 👇
{product} • {₹price}
{hrs}
≈ {days} work-days • {months} • @ {₹/hr}/hr
{verdict plain text}
• {hours} work hours
• {days} work days
• {₹/hr} ₹/hour
Monthly salary ka kitna %? 😭
{salary % line}
Is paise me aur kya aa sakta tha? 👀
{equivalents plain text}
— via Should I Buy It? India 🇮🇳
```

## 6. Status Codes
No HTTP statuses. Internal outcomes:
- `ok` — calculated + rendered
- `invalid_input` — price missing/invalid → alert, nothing written
- `storage_blocked` — localStorage throws → caught, calc continues
- `clipboard_denied` — fallback to `alert(text)`
- `png_failed` — `Try again 🔁`, no download
- `formsubmit_rejected` (incl. `file://`) — honest message + `mailto:` fallback
- `throttled` — idea resend within 30s → hold message, no request

## 7. Error Rules
- Consistent UX: invalid → alert; clipboard fail → alert text; storage fail → silent + calc continues; send settles → button re-enables in `finally`.
- Never show stack traces, file paths, or browser internals to users.
- Log to console only during dev; strip logs before release. Never log salary/history to server (no server).

## 8. Rate Limiting
- Calc/history: none needed (synchronous, local).
- Idea form: 30s client throttle (`lastFeatSentAt`) + disabled-while-posting; FormSubmit applies its own server-side controls.

## 9. Third-Party APIs
| Provider | Purpose | Env | Usage |
|----------|---------|-----|-------|
| Google Fonts | Baloo 2 + Fredoka | none | `<link>` stylesheet + gstatic preconnect; offline → system-ui fallback |
| FormSubmit | Idea emails to owner | none (address is routing, not secret) | `POST /ajax/<owner>` with `_subject`, `_template: table`, `_captcha: false`; requires hosting + activation |
| Clipboard API | Copy list message | none | `navigator.clipboard.writeText`; insecure-context fallback → `alert` |
| Canvas 2D | PNG share image | none | built-in browser API, fully offline |

No Stripe (`STRIPE_SECRET_KEY` server-only if ever added — not present). No WhatsApp integration (Save Hisab replaced it). Document new integration here before coding.

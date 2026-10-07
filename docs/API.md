# API.md — Should I Buy It? Indian Edition

## 1. Purpose
No backend REST/GraphQL/tRPC. Frontend-only. This file documents internal JS "API" (functions) + browser/external integrations so agents don't invent `/api/v1/projects`.

## 2. Base Configuration
- Backend base URL: None. App runs from `file://` or any static host.
- Response format: DOM updates, not JSON over HTTP.
- Versioning: None. Single `index.html`.

## 3. Authentication
None. No headers, tokens, or `Authorization: Bearer`. Never expose server credentials — none exist.

## 4. Endpoint Conventions (internal functions, not HTTP)
Use these instead of REST for V1:
```
calculate()                 # () => void — reads inputs, updates #resultBox, saves history
verdict(hours, days, price, salary) -> string (HTML)
equivalents(price) -> string (HTML)
renderHist(hist)            # Array<HistoryEntry> => void
fmtIN(n) -> "₹79,900"
buildRing() (IIFE)          # renders 20 emoji items once
```
- Nouns for resources N/A; keep function names stable. Don't add `/api/*` routes unless backend approved.

## 5. Example "Endpoint" — calculate()
**Trigger:** click `#calcBtn` or Enter in inputs or preset tap or history ↩
**Input (DOM, not JSON body):** `{product:string, price:number, mode:'monthly'|'hourly', salary|hourly, hrsPerDay, daysPerMonth, savePct}`
**Success (DOM):**
- `#rProduct: "iPhone 16 • ₹79,900"`
- `#rHours: "1780 hrs"`, `#rDays: "≈ 198 work-days • 7.6 months savings • @ ₹150/hr"`
- `#rVerdict: 🔴 RUK JAO!...`, stats, bar width, `#rEquiv`
- History prepended, share handlers bound
**Validation error:** `alert('Price daalo yaar!')`, no DOM change.

Share text (used by both share actions):
```
🤔 Should I Buy It?
{product} — {₹price}
⏱️ {hours} ghante kaam ({days} days)
💰 Meri rate: {₹/hr}/hr
{🔴 Mehenga hai... | 🟢 Affordable hai!}
— via Should I Buy It? India
```

## 6. Status Codes
No HTTP statuses. Internal outcomes:
- `ok` — calculated + rendered
- `invalid_input` — price missing/0 → alert
- `storage_blocked` — localStorage throws → caught, calc continues
- `clipboard_denied` — fallback to `alert(txt)`

## 7. Error Rules
- Consistent UX: invalid → alert; clipboard fail → alert text; storage fail → silent + calc continues.
- Never show stack traces, file paths, or browser internals to users.
- Log to console only during dev; strip logs before release. Never log salary/history to server (no server).

## 8. Rate Limiting
N/A — synchronous local calc, no abuse surface. If backend added (e.g., AI verdicts), rate-limit generation/share endpoints.

## 9. Third-Party APIs
| Provider | Purpose | Env | Usage |
|----------|---------|-----|-------|
| Google Fonts | Baloo 2 + Fredoka | none | `<link href="https://fonts.googleapis.com/css2?family=Baloo+2...&family=Fredoka...">` client CSS only; offline → fallback fonts |
| wa.me | WhatsApp share | none | `window.open('https://wa.me/?text='+encodeURIComponent(txt),'_blank')` triggered by `#waBtn`; needs internet; no key/webhook |
| Clipboard API | Copy share text | none | `navigator.clipboard.writeText(txt)` in `#copyBtn`; requires secure context; fallback `alert` |

No Stripe (`STRIPE_SECRET_KEY` server-only if ever added → webhook `/api/webhooks/stripe` — not present V1). Document new integration here before coding.

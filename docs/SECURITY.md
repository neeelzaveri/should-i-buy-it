# SECURITY.md — Should I Buy It? Indian Edition

## 1. Purpose
Client-only static app. No auth, no backend, no secrets. These rules keep inputs safe, history private, and agents from adding risky integrations.

## 2. Authentication & Authorization
- None. No login, no roles, no protected routes. All content is public static + user-local inputs.
- Never add auth to "make a feature work" without user approval + PRD/TRD update.
- Never trust client values for anything server-side — there is no server. Clamp numbers for correct math, not for access control.

## 3. Secrets & Environment Variables
- No secrets exist. Do not introduce API keys, `.env`, or `DATABASE_URL`.
- Never hardcode keys in `index.html`. Never commit `.env` (not needed).
- `.env.example` not needed for V1. If backend added later, create it with names only:
```
# Example only — V1 uses none
DATABASE_URL=
STRIPE_SECRET_KEY=
NEXT_PUBLIC_APP_URL=
```
- Dev/prod use same static file; no separate credentials.

## 4. Input Validation
- All inputs user-controlled: `product` (string), `price/salary/hrsPerDay/daysPerMonth/savePct/hourly` (numbers).
- Rules in `calculate()` (`index.html:398-415`): `Number(x||0)`, `Math.max(1,...)` for price/salary/rates, clamp `hrsPerDay 1–16`, `daysPerMonth 1–31`, `savePct 5–100`.
- Validate even though HTML has `min/max` — user can edit DOM.
- Reject NaN/empty price with `alert`, abort calc. Trim product, fallback `'Ye product'`.
- No Zod/server validation — no server. Keep clamping logic if refactoring.

## 5. API Security
- No backend endpoints. No sessions/tokens. No rate limiting needed (instant local calc).
- Only outbound: Google Fonts stylesheet + `wa.me/?text=` on click. No credentials in URLs.
- Do not expose stack traces — no server errors to leak. JS errors stay in console.
- HTTPS recommended when hosting (Vercel/Netlify default), but `file://` also works.

## 6. Data Protection
- Store only what product needs: `sibi_hist` = last 5 `{product, price, hours}`. No salary stored. No PII transmitted.
- Do not log passwords/tokens (none exist). Do not store salary in localStorage.
- No SQL/ORM — no injection surface except DOM. Use `textContent` for user strings where possible; current `innerHTML` for verdict/equiv/history is built from numbers + static copy — keep it that way, never `innerHTML = product` raw without escaping.
- Share only on explicit button click via `encodeURIComponent`.

## 7. Error Handling
User errors show `alert` or inline empty/history text. Must never reveal:
- database credentials (none), API keys (none), stack traces, internal file paths, other users' data (no other users)
- Clipboard failure → `alert(txt)` fallback is intentional, contains only user's own calc.

## 8. AI Agent Rules
Must:
- Never invent/hardcode production credentials or analytics IDs.
- Never disable validation/clamping to "fix" calc.
- Never bypass same-origin safety (no `innerHTML` injection of raw inputs, no `eval`).
- Never exfiltrate salary/history to external URL.
- Never add backend, auth, tracking, or CDN JS without approval.
- Ask for clarification when request conflicts with this file (e.g., "add login + cloud history" needs PRD/ARCHITECTURE/security review first).

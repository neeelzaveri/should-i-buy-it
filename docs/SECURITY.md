# SECURITY.md — Should I Buy It? Indian Edition

## 1. Purpose
Client-only static app. No auth, no backend, no secrets. These rules keep inputs safe, history private, ideas creator-only, and agents from adding risky integrations.

## 2. Authentication & Authorization
- None. No login, no roles, no protected routes. All content is public static + user-local inputs.
- Never add auth to "make a feature work" without user approval + PRD/TRD update.
- Never trust client values for anything server-side — there is no server. Guard + clamp numbers for correct math, not for access control.

## 3. Secrets & Environment Variables
- No secrets exist. Do not introduce API keys, `.env`, or `DATABASE_URL`.
- Never hardcode keys in `index.html`. Never commit `.env` (not needed).
- The owner email in JS is a form-routing address, not a secret — but expect harvester spam because of it.
- `.env.example` not needed. If backend added later, create it with names only.
- Dev/prod use same static file; no separate credentials.

## 4. Input Validation
- All inputs user-controlled: `product` (string), `price/salary/hrsPerDay/daysPerMonth/savePct/hourly/hourlyHrs/savePct2` (numbers).
- Price: explicit empty/NaN/≤0 guard → `alert`, abort, zero side effects.
- Clamps: salary/hourly ≥1, `hrsPerDay` 1–24, `daysPerMonth` 1–31, `savePct` 5–100.
- Validate even though HTML has `min/max` — user can edit DOM or type past spinners.
- No Zod/server validation — no server. Keep guard + clamps if refactoring.

## 5. API Security
- No backend endpoints. No sessions/tokens. Calc needs no rate limiting (instant, local).
- Outbound only: Google Fonts stylesheet, FormSubmit AJAX on explicit Send (with `success`-field check + 30s client throttle).
- FormSubmit rejects `file://` pages — code detects it and falls back honestly instead of claiming delivery.
- Do not expose stack traces — no server errors to leak. JS errors stay in console (and there is no logging code).
- HTTPS required when hosted (host default); `file://` works for everything except email auto-send.

## 6. Data Protection
- Store only what product needs: `sibi_hist` = last 5 `{product, price, hours}`. No salary stored. No PII transmitted except explicit Copy text / PNG the user saves.
- Ideas are emailed, never stored, never displayed — old `sibi_features` key is purged on load.
- Do not log passwords/tokens (none exist). Do not store salary in localStorage.
- No SQL/ORM — no injection surface except DOM. User strings render via `textContent` (history rows, ring emojis, PNG text); email HTML is escaped. `innerHTML` is used only for verdict/equiv output built from numbers + static copy — marked with SAFE SINK comments; never pass raw user strings there.

## 7. Error Handling
User errors show `alert` or inline empty/history/modal status text. Must never reveal:
- database credentials (none), API keys (none), stack traces, internal file paths, other users' data (no other users)
- Clipboard failure → `alert(text)` fallback is intentional, contains only user's own calc.

## 8. AI Agent Rules
Must:
- Never invent/hardcode production credentials or analytics IDs.
- Never disable validation/clamping to "fix" calc.
- Never bypass same-origin safety (no `innerHTML` injection of raw inputs, no `eval`).
- Never exfiltrate salary/history/ideas anywhere except the owner inbox on explicit Send.
- Never add backend, auth, tracking, libraries, or CDN JS without approval.
- Ask for clarification when request conflicts with this file (e.g., "add login + cloud history" needs PRD/ARCHITECTURE/security review first).

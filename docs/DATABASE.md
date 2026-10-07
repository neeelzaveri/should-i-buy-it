# DATABASE.md — Should I Buy It? Indian Edition

## 1. Purpose
No server database. Only browser `localStorage`. This file documents that so agents don't invent PostgreSQL/Prisma/Supabase.

## 2. Database Stack
- Primary: None (static file)
- Client storage: `localStorage` key `sibi_hist`
- ORM / Migrations: None — N/A
- Provider: Browser only (Chrome/Edge/Firefox/Safari)

## 3. Environment
- No `DATABASE_URL`, no connection strings. Nothing to configure.
- Dev/staging/prod share same file; history is per-browser, per-device, never synced.
- Never hardcode production DB URL — there isn't one. If backend added later, env vars required + SECURITY.md review.

## 4. Core Models (actual)
### HistoryEntry (stored in `sibi_hist` array, max 5)
- `product: string` — trimmed product name, e.g. `"iPhone 18 Pro"`
- `price: number` — validated >0, e.g. `164900`
- `hours: number` — rounded work-hours, e.g. `3674`
- Example: `[{product:"Royal Enfield", price:193000, hours:4300}, ...]`
- No `id`, no timestamps, no userId — single-user local list. Order = recency (unshift).
- Rendered via `textContent` only; malformed rows degrade via `(h&&...)` guards.

Not stored (intentionally): salary, hours/day, save %, phone, email, ideas. Salary is input-only, never persisted. Ideas go to email, never storage (legacy `sibi_features` key is purged on load).

Relationship: No relations. One flat array. Reload (↩) refills `#product/#price` and recomputes with current salary — stored `hours` is display-only, not reused for math.

## 5. Schema Rules
- Stable shape: always array of `{product,price,hours}`; guard `JSON.parse` with try/catch + `||'[]'`.
- Cap at 5 (`slice(0,5)`) to bound storage; write only when `saveHistory` is true.
- Unique constraints/indexes: N/A (tiny array, linear render).
- Don't duplicate facts: salary lives in inputs, not storage; hours derived, stored only for history label.

## 6. Migrations
None. If shape changes (e.g., add `date`):
1. Bump reader to handle old `[{product,price,hours}]` + new fields
2. Test with old data in localStorage
3. No server migration, no deploy script
Never touch another user's data — there is none; only own browser.

## 7. Seed Data
None. Inputs are placeholder hints with JS fallbacks, not DB seeds. Do not ship real user data in code.

## 8. Production Safety
- No backups needed (ephemeral, recreatable). Clearing browser clears history — expected.
- No destructive schema ops; `localStorage.setItem` wrapped in try/catch (private mode).
- Use atomic `setItem(JSON.stringify(hist))` after slice; no transactions needed.
- Follow SECURITY.md: never store secrets/PII, never sync to server without consent + doc updates.

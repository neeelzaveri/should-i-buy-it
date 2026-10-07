# Implementation Plan — Should I Buy It? Indian Edition

> Status: V1 built + hardened as single `index.html`. Use this plan for maintenance and V2. Build one phase at a time, verify before moving on.

## Project Rule
Small diffs to `index.html` only. List files to change before coding. Update docs when formulas, copy, or structure change.

## Phase 0 — Project Setup (done)
Tasks:
- Create `index.html` with `<style>` + body + `<script>`
- Add `docs/` with 12 md files
- No tooling
Deliverable: double-click `index.html` opens with badge empty state, no errors
Verify: fresh open shows empty state (no auto-calc, no alert), no console output

## Phase 1 — Shell + Design System (done)
Tasks:
- Topbar (logo + pill + idea tile), hero ring (20 emojis incl. 📱, spin/counter-spin, resize-aware), two cards, footer, floating dots
- Apply tokens: paper/ink/green-dark/orange/yellow, Baloo 2 + Fredoka, 3px borders, hard shadows
Deliverable: static layout matches DESIGN_SYSTEM.md at 1100px + 360px
Verify: no overflow at 320px; ring centered, never overlaps headline; sticker/CTA visible

## Phase 2 — Input + Presets (done)
Tasks:
- Product/price inputs (placeholders, no defaults), 10 current-price Indian presets, mode seg toggle, monthly/hourly boxes (hourly incl. hours/day)
- Enter-key handler (9 inputs), explicit price guard, clamps (hrs 1–24, days 1–31, save 5–100)
Deliverable: preset tap fills + calculates + saves + scrolls; mode toggle swaps boxes
Verify: each preset calculates; hourly vs monthly rates differ; Enter works; empty price alerts with zero side effects

## Phase 3 — Calculation Engine (done, high-risk on change)
Tasks:
- `calculate(saveHistory)`, `fmtIN`, hourly/effective/hours/days/months math, honest bar width, `lastHisab` snapshot
- `alert` on empty/NaN/≤0 price
Deliverable: correct hours for known vectors (see TESTING.md)
Verify: 35000/(26*9)≈149.57/hr; 69900 at 30% save ≈ 1558 hrs — recompute, don't hardcode old numbers

## Phase 4 — Verdict + Equivalents + Stats (done)
Tasks:
- `verdict()` 6 tiers, `equivalents()` chai/vada pav/auto/movie/rent + 24 rotating advice lines, stats + bar + `%` text
Deliverable: each tier reachable (test <2h, <8h, <3d, <1mo, <3mo, ≥3mo); advice matches verdict tier
Verify: boundary values show right emoji/copy; bar width = price/salary*100; advice rotates per calc

## Phase 5 — History + Share (done)
Tasks:
- `sibi_hist` (max 5), XSS-safe `renderHist()`, ↩ reload without duplicating
- Save Hisab PNG (`drawHisab` canvas, footer, truncation) + list-format Copy text + fallbacks
Deliverable: refresh persists 5; ↩ recalculates cleanly; PNG downloads; copy order matches card
Verify: 6th calc drops oldest; private-mode (localStorage blocked) still calculates; long names never break card or PNG

## Phase 6 — Production Readiness (done, then extended)
Tasks:
- Responsive pass (320/360/768/1100), accessibility pass (labels, traps, contrast, heading order), Hinglish copy review
- Security review: XSS-safe history, SAFE SINK comments, FormSubmit success-check, 30s throttle, `finally` re-enable
- SEO basics: description, OG tags, ⏰ favicon, gstatic preconnect
- Manual TESTING.md run
Deliverable: release-ready static file
Verify: complete TESTING.md checklist, no `console.*`, footer year 2026

## Phase 7 — Post-V1 Upgrades (done)
- Placeholders everywhere (no prefilled values, no load-time auto-calc)
- Hourly hours/day field; 24h cap; current Zomato/fresher/freelance hints
- iPhone 18 Pro / MacBook M4 / 22K-gold price corrections
- Feature modal: FormSubmit HTML email + throttle + required name + mailto fallback
- Disclaimer modal (6 bullets) moved to input-card link; footer keeps creator + socials
- Removed: EMI section, WhatsApp share, public idea list, dead CSS
Deliverable: docs match code (this refresh)
Verify: re-run full TESTING.md

## Out of Scope for V1
- Auth, backend, cloud DB, multi-device sync, custom domains, team accounts
- EMI/SIP calculators (removed), price tracking, native apps, dark mode, i18n beyond Hinglish
- Analytics, ads, A/B testing, automated test runner

## Working With the Plan
1. Give agent only current phase + relevant docs
2. Ask for file/line plan (e.g., "will edit `index.html` calc block only")
3. Implement, manually verify, fix before next phase
4. Update TRD/APP_FLOW/TESTING if behavior changed

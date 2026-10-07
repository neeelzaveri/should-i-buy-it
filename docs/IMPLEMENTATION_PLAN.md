# Implementation Plan — Should I Buy It? Indian Edition

> Status: V1 already built as single `index.html`. Use this plan for maintenance and V2. Build one phase at a time, verify before moving on.

## Project Rule
Small diffs to `index.html` only. List files to change before coding. Update docs when formulas, copy, or structure change.

## Phase 0 — Project Setup (done)
Tasks:
- Create `index.html` with `<style>` + body + `<script>`
- Add `docs/` with 12 md files
- No tooling
Deliverable: double-click `index.html` runs with demo auto-calc
Verify: fresh open shows 79900 iPhone result, no console errors

## Phase 1 — Shell + Design System (done)
Tasks:
- Topbar, hero ring (20 emojis, spin/counter-spin), two cards, footer, floating dots
- Apply tokens: paper/ink/green/orange/yellow, Baloo 2 + Fredoka, 3px borders, hard shadows
Deliverable: static layout matches DESIGN_SYSTEM.md at 1100px + 360px
Verify: no overflow at 320px; ring centered; sticker/CTA visible

## Phase 2 — Input + Presets (done)
Tasks:
- Product/price inputs, 10 Indian presets, mode seg toggle, monthly/hourly boxes
- Enter-key handler, input clamping
Deliverable: preset tap fills + calculates + scrolls; mode toggle swaps boxes
Verify: each preset calculates; hourly vs monthly rates differ; Enter works

## Phase 3 — Calculation Engine (done, high-risk on change)
Tasks:
- `calculate()`, `fmtIN`, hourly/effective/hours/days/months math, bar %
- `alert` on invalid price
Deliverable: correct hours for known vectors (see TESTING.md)
Verify: 35000/(26*9)≈149.57/hr; 79900 at 30% save ≈ 1780 hrs — recompute, don't hardcode old numbers

## Phase 4 — Verdict + Equivalents + Stats (done)
Tasks:
- `verdict()` 5 tiers, `equivalents()` chai/vada pav/auto/movie/rent, stats + bar + `%` text
Deliverable: each tier reachable (test <2h, <8h, <3d, <1mo, <3mo, ≥3mo)
Verify: boundary values show right emoji/copy; bar width = min(100, price/salary*100)

## Phase 5 — History + Share (done)
Tasks:
- `sibi_hist` (max 5), `renderHist()`, ↩ reload, wa.me + clipboard + fallback
Deliverable: refresh persists 5; ↩ recalculates; share text matches TRD format
Verify: 6th calc drops oldest; private-mode (localStorage blocked) still calculates

## Phase 6 — Production Readiness (current)
Tasks:
- Responsive pass (360/768/1100), accessibility pass (labels, focus, heading order), Hinglish copy review
- Manual TESTING.md run, fix overflow/clipboard issues
Deliverable: release-ready static file
Verify: complete TESTING.md checklist, no `console.log`, footer year 2026

## Out of Scope for V1
- Auth, backend, cloud DB, multi-device sync, custom domains, team accounts
- EMI/SIP calculators, price tracking, native apps, dark mode, i18n beyond Hinglish
- Analytics, ads, A/B testing, automated test runner

## Working With the Plan
1. Give agent only current phase + relevant docs
2. Ask for file/line plan (e.g., "will edit `index.html:398-449` only")
3. Implement, manually verify, fix before next phase
4. Update TRD/APP_FLOW/TESTING if behavior changed

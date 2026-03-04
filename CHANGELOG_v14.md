# Navigator v14 — Change Log
**Date:** 3 March 2026  
**Source:** v13 + v14 audit-driven fixes  
**True Completion Rate:** 51/55 fully implemented (93%)

---

## v14 Changes (this release)

### ✅ CR-009 — Friction Map (Page 5): Hidden from nav sidebar
- `display:none` applied to `nav-item[data-page="5"]`
- Engine code preserved untouched (renderPage5, PARKED_PAGES entry)
- Previously: visible in sidebar but showed 🚧 placeholder when clicked

### ✅ CR-034 — Workforce Transition (Page 13): Hidden from nav sidebar  
- `display:none` applied to `nav-item[data-page="13"]`
- Engine code preserved untouched (workforce.py, renderPage13)
- Previously: same issue as #9

### ✅ CR-033 — Per-BU Location Sliders (BU Impact Page 17)
- Replaced read-only per-BU distribution table with interactive per-BU sliders
- Consumer, Business, Enterprise each have separate onshore/nearshore/offshore sliders
- Sliders auto-normalise to 100% within each BU
- Delta indicator shows target vs current (▲/▼)
- Mini composition bar per BU
- BU vs Global comparison table shows deviation from global target
- `window._buLocMixState` initialised from queue-level location data

### ✅ CR-037 — OM Cost/Effort Bridge Chart + Auto-Enable Button (Page 20)
- **Bridge chart**: Replaced 3-box layout with proper waterfall bar visualisation showing step-by-step: Current Cost → FTE Reduction → Location Arbitrage → Target Cost
- Each bar labelled with $ value, proportionally scaled to current total
- Summary metric tiles below the waterfall (Headcount Savings / Location Arbitrage / Total)
- **Auto-Enable CTA**: "Auto-Enable N Needed Initiatives + Recalculate" button activates all Location Strategy initiatives flagged as needed for the target OM mix, then calls `recalculateAll()`
- Needed initiatives highlighted in amber in the table

### 🗂️ Changelog housekeeping
- **Deleted CHANGELOG_FINAL.md** — contained false 100% completion claims (confirmed by audit)
- Source of truth: CHANGELOG_CORRECTED.md + this file + audit document

---

## Items accepted as-is from audit

| # | Item | Decision |
|---|------|----------|
| 47 | Secondary lever impacts in modal | Already implemented in v13 (Multi-Lever Breakdown shown in modal preview when `_secondaryFTE > 0`). Audit may have reflected earlier code state. |
| 52 | BU filter on NPV/ROI/payback | Accepted as interim. Requires server-side waterfall engine changes to accept BU scope parameter. Warning banner on Page 14 remains. |

---

## Remaining open items (from v13 audit)

| # | Description | Effort |
|---|-------------|--------|
| 52 | BU-segmented financial outputs (NPV, ROI, payback) — server-side waterfall engine refactor | 1–2 days |

---

## v13 baseline (carried forward)
- 46 of 55 change requests fully implemented
- 5 partial/with caveats
- 4 open (now reduced to 1 post v14)

PARKED_PAGES: 5 (Friction Map), 13 (Workforce Transition), 15 (Overrides) — engine code preserved, hidden from nav

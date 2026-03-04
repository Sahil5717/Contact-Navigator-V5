# Navigator v12 — Phase 7 Complete Changelog

## Session Summary
**10 bugs/features implemented** across 6 files, moving from 55% → 73% completion.

---

## Phase 7A: Critical Bug Fixes (3/3)

### #44 — Recommendation Linkage Field Mismatch ✅
**File:** `engines/recommendations.py` (lines 155-180)
- **Problem:** Frontend reads `r.trigger_details` (array) and `r.savings_display` (string) but API returned `r.rationale` (string) and `r.annual_saving` (number). Caused blank columns on Pages 4, 6, 7.
- **Fix:** Added `trigger_details` array (extracts signal descriptions from SIGNAL_TO_LEVER mapping) and `savings_display` string (formats as "$X.XM/yr" or "$XK/yr").

### #45 — Sub-Intent Pipeline Structure Mismatch ✅
**File:** `templates/index.html` (line ~1348)
- **Problem:** Backend returns nested `[{intent, subIntents: [...]}]` but frontend reads flat `[{parentIntent, subIntent}]`. Caused "undefined" on Pages 8, 9, 18.
- **Fix:** Added `_getSubIntentsFlat()` utility with auto-detection (flat pass-through vs nested flattening). Updated 2 consumer sites.

### #55 — Queue Analysis Data Quality ✅
**File:** `templates/index.html` (lines 7970-7992, 7569, 7766, 7770, 8014, 8019)
- **Problem:** CSAT showing 357% (pre-multiplied), Location/Sourcing hardcoded to 'Onshore'/'In-house', Complexity showing raw decimals.
- **Fix:** CSAT auto-detects pre-multiplied values (>5 → ÷100), shows as "X.X/5" with RAG. Location/Sourcing show "—" when missing. Complexity shows Simple/Moderate/Complex labels. Replaced all `'Onshore'` fallbacks with `'Unspecified'`.

---

## Phase 7B: Engine Enhancements (3/3)

### #50 — Wire Gantt Edits to Waterfall Recalculation ✅
**File:** `templates/index.html` (line ~6123)
- **Problem:** Gantt start/duration edits only re-rendered the chart — waterfall numbers, NPV, FTE totals never updated.
- **Fix:** `_ganttEdit()` now calls `runWaterfall()` after field updates, reassigns global `WATERFALL` object, and shows a toast with delta NPV + FTE impact. Footer text updated to "waterfall recalculates automatically".

### #49 — Initiative-Driven Channel Migrations ✅
**Files:** `engines/channel_strategy.py`, `app.py`
- **Problem:** Channel migration decisions were purely health-threshold-gated. A Voice queue with good CSAT but active deflection initiatives would stay at "maintain".
- **Fix:** `run_channel_strategy()` now accepts `initiatives` parameter. Computes deflection impact per channel from enabled initiatives with deflection/self_service/channel_shift/automation levers. Overrides `maintain` → `migrate_from` when initiative-driven deflection exceeds health-only threshold. Both `app.py` call sites updated.

### #43 — Wire Maturity Scores into Readiness, Recommendations, Waterfall ✅
**Files:** `engines/readiness.py`, `engines/recommendations.py`, `app.py`
- **Problem:** Maturity scores existed in STATE['maturity'] but were completely disconnected from the three engines.
- **Fix:**
  - **readiness.py:** `compute_readiness()` accepts `maturity` parameter. Technology + Data maturity modulates AI & Automation readiness (0.4 + 0.6×factor). Process + People maturity modulates Operating Model readiness. Stores `maturityFactors`, `maturityDimensions` in context for downstream use.
  - **recommendations.py:** `_detect_signals()` generates `maturity_gap` signals when overall maturity < 75% of target or any dimension < 2.5/5.
  - **app.py:** Both load and recompute paths pass `STATE['maturity']` to `compute_readiness()`.

---

## Phase 7C: Feature Additions (4/4)

### #42 — Expand Maturity Questions to 5-8 per Dimension ✅
**File:** `templates/index.html`
- **Before:** 13 questions (2-3 per dimension) — too coarse for meaningful scoring.
- **After:** 34 questions (6-8 per dimension) covering strategy, process, technology, people, and governance per dimension.
- Question counts: Channel Strategy (7), Automation & AI (8), Workforce Model (7), Analytics (6), Customer Experience (6).
- Scoring updated: now uses `(total / maxPossible) × 4` for decimal precision (e.g., 2.3/4 instead of just 2 or 3).
- Drill-down modal enhanced with "Assessment Questions" section showing per-question scores with mini progress bars.

### #46 — Sub-Intent Cost Breakdown Table ✅
**File:** `templates/index.html` (Page 7 — Cost Analysis)
- **New section:** "Sub-Intent Cost Breakdown" table after Top 10 Expensive Queues.
- Shows per-sub-intent: parent intent, volume share, complexity, estimated CPC (intent avg × complexity multiplier), vs benchmark %, monthly cost, excess spend.
- Grouped by intent with visual separators. Complexity-adjusted CPC: Simple ×0.7, Moderate ×1.0, Complex ×1.4.
- Depends on #45 (`_getSubIntentsFlat`) — gracefully shows "not available" message if no sub-intent data.

### #48 — Layer-Specific Initiative Edit Modals ✅
**File:** `templates/index.html` (Initiative Editor)
- **AI & Automation:** Tech Readiness (legacy/hybrid/cloud/composable), AI Model Type (rules/ML/GenAI), Data Dependency (low/medium/high).
- **Operating Model:** Process Scope (single/team/operation/enterprise), Tier Structure Change (none/add/merge/redesign), Change Mgmt Intensity, BU Mix % split.
- **Location Strategy:** Migration Direction (offshore/nearshore/reshore), Cost Arbitrage %, Target Geography (India/Philippines/LATAM/E.Europe), Language Requirements, Transition Model (lift&shift/phased/hybrid).
- Fields render conditionally based on `init.layer`. Values stored on initiative objects for downstream use.

### #54 — Sub-Intent Expandable Rows in Queue Analysis ✅
**File:** `templates/index.html` (Page 19 — Queue Analysis)
- Queue table rows now show ▸ expand button when sub-intents exist for that intent.
- Clicking expands child rows showing: sub-intent name, volume allocation, feasibility score, deflectable channels, and complexity.
- Rows styled with indentation (└ prefix), lighter background, and smaller font to visually nest under parent.
- Depends on #45 (`_getSubIntentsFlat`) — no expand buttons shown when sub-intent data absent.

---

## Files Modified (6 total)

| File | Lines | Changes |
|------|-------|---------|
| `app.py` | 1,047 | #43 maturity→readiness wiring, #49 initiatives→channel_strategy |
| `engines/readiness.py` | 384 | #43 maturity-adjusted readiness map |
| `engines/recommendations.py` | 384 | #44 trigger_details/savings_display, #43 maturity_gap signals |
| `engines/channel_strategy.py` | 558 | #49 initiative-driven deflection overlay |
| `engines/waterfall.py` | 1,278 | (unchanged — referenced by #50 client-side) |
| `templates/index.html` | 8,693 | #45, #55, #50, #42, #46, #48, #54 |

## Cumulative Audit Status
- **Completed:** 37/55 (67%) — up from 30/55 (55%)
- **Partial:** 6/55 (11%)
- **Pending:** 12/55 (22%)

# Navigator v12 — Phase 7 Complete Changelog (All Sessions)

## Summary
**17 items resolved** across 6 files. Audit completion: **44/55 (80%)**.

---

## Phase 7A: Critical Bug Fixes ✅

| # | Fix | Files |
|---|-----|-------|
| **#44** | Recommendation `trigger_details[]` + `savings_display` fields added to API | `recommendations.py` |
| **#45** | Sub-intent nested→flat adapter (`_getSubIntentsFlat`) | `index.html` |
| **#55** | Queue CSAT ÷100 fix, location/sourcing "—" fallback, complexity labels | `index.html` |

## Phase 7B: Engine Wiring ✅

| # | Fix | Files |
|---|-----|-------|
| **#50** | Gantt edits → `runWaterfall()` + delta toast | `index.html` |
| **#49** | Initiative-driven channel migrations (deflection overlay) | `channel_strategy.py`, `app.py` |
| **#43** | Maturity → readiness modulation + recommendation gap signals | `readiness.py`, `recommendations.py`, `app.py` |

## Phase 7C: Feature Additions ✅

| # | Fix | Files |
|---|-----|-------|
| **#42** | Maturity questions 13→34 (6-8/dimension), decimal scoring | `index.html` |
| **#46** | Sub-intent cost breakdown table (Page 7) | `index.html` |
| **#48** | Layer-specific initiative editor (AI/OM/Location configs) | `index.html` |
| **#54** | Expandable sub-intent rows in Queue Analysis (Page 19) | `index.html` |

## Phase 7D: Polish & Navigation ✅

| # | Fix | Files |
|---|-----|-------|
| **#51** | "+N more initiatives" → clickable expand/collapse toggle | `index.html` |
| **#41** | Compact recommendation table (`_buildCompactRecTable`) DRYs 3 pages | `index.html` |
| **#37** | OM sliders invalidate BU Impact, Channel Strategy, Waterfall pages | `index.html` |
| **#52/#53** | Nav reorder: DIAGNOSE → SIZE → SOLVE → JUSTIFY. Sub-Intent/Queue moved to Diagnose, Operating Model to Size | `index.html` |

## Items Confirmed Already Done (reclassified)

| # | Status | Notes |
|---|--------|-------|
| **#32** | ✅ Already implemented | Per-BU pool utilization bars exist in BU Impact |
| **#36** | ✅ Already implemented | Sub-intent inline editors (volume, complexity, automation) with `_siEdit` handler |
| **#27** | ✅ Already implemented | CX Safeguard gates with CSAT<65%/FCR<60% thresholds + blocking actions |
| **#8** | ✅ Functional | Composite friction scoring (0.35×repeat + 0.25×escalation + 0.25×CES + 0.15×FCR) with hotspot detection |
| **#26** | ✅ Fixed by #49 | Sankey now benefits from initiative-driven channel strategy wiring |

---

## Navigation Structure (New)

```
🔍 DIAGNOSE
  1  Executive Summary
  2  Benchmarking
  3  Maturity Assessment
  4  Performance Heatmap
  🚧 Friction Map (parked)
  6  Gap Analysis
  7  Cost Analysis
  19 Queue Analysis
  18 Sub-Intent Analysis

📐 SIZE THE PRIZE
  ★  Opportunity Buckets
  8  Self-Service Feasibility
  20 Operating Model

💡 SOLVE
  9  Initiative Roadmap
  10 Projected Channel Mix
  11 Implementation Gantt

🎯 JUSTIFY
  14 Impact Dashboard
  17 BU Impact Views
  12 Risk Assessment
  🚧 Workforce Transition (parked)
  21 Scenario Comparison
```

## Files Modified

| File | Lines | Key Changes |
|------|-------|-------------|
| `templates/index.html` | 8,704 | Nav reorder, compact rec tables, expandable +N, sub-intent costs, layer modals, queue sub-intent rows, maturity questions, OM wiring |
| `app.py` | 1,047 | Maturity→readiness, initiatives→channel_strategy |
| `engines/readiness.py` | 384 | Maturity modulation |
| `engines/recommendations.py` | 384 | trigger_details, savings_display, maturity gaps |
| `engines/channel_strategy.py` | 558 | Initiative deflection overlay |

## Remaining Items (11/55 = 20%)

**Parked/Low priority:**
- #5 Friction Map (parked — scoring under validation)
- #13 Workforce Transition (parked — data outputs)
- #15 Overrides page (replaced by Data Management)
- #21 Visual differentiation per diagnostic rec panel (nice-to-have)
- #24/#25 Introduce rows volume/health columns show dashes (edge case)
- #33 Location sliders in BU Impact (OM page has them)
- #47 Scenario Comparison page (scaffold exists)

**Would benefit from server-side work:**
- PDF export enhancement
- Benchmark override persistence
- BU filter propagation across pages

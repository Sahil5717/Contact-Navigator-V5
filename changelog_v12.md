# EY Contact Navigator — v12 Changelog

## Packet 6: Login Page (Change #39)
**Date:** 2026-03-01  
**Type:** Frontend  

### Change #39 — Login Page Redesign
- **BEFORE:** No login.html template existed (route referenced it but file was missing)
- **AFTER:** Professional branded landing experience:
  - Split layout: left branding panel (60%), right login form (40%)
  - Left panel: EY mark + "Contact Navigator" logo, punchy headline ("Transform your contact centre from cost centre to value engine"), feature highlights grid (Diagnostic, Size the Prize, Solve, Justify), stats bar (15 modules, 6 pools, 40+ initiatives, 3 templates)
  - Right panel: Clean login form with username/password, demo credentials hint, copyright 2026
  - Dark EY-branded gradient with subtle radial glows (yellow top-right, blue bottom-left)
  - Responsive: stacks vertically on mobile/tablet
  - Proper error handling with inline error messages
  - Loading state on submit button
  - Copyright updated to 2026

---

## Packet 5: Business Case Pages (Changes #29, #30-33, #38)
**Date:** 2026-03-01  
**Type:** Engine + Frontend  

### Change #29 — Impact Dashboard SCR Redesign (Page 14)
- **BEFORE:** Operational dashboard layout with scattered KPI cards + tables
- **AFTER:** Consulting-grade Situation → Complication → Resolution narrative
  - Headline answer box with full-sentence investment recommendation
  - SITUATION section: Current state cost baseline with growth projections
  - COMPLICATION section: Cost of Inaction with year-by-year avoidable cost table
  - RESOLUTION section: Transformation plan with FTE/savings storyline
  - Retained: FTE waterfall, NPV chart, sensitivity tornado, audit trail, CX impact

### Change #30 — BU Impact: Initiative Attribution (Page 17)
- Each BU card now includes "Initiative Drivers" drilldown
- Per-initiative FTE attribution with ID, name, layer badge
- Engine: `bu_impact[bu].initiative_attrib[]` populated during waterfall cascade

### Change #31 — BU Impact: Incremental Year-over-Year Table (Page 17)
- **BEFORE:** Cumulative FTE (Y3 read as "363 reduced in Y3 alone")
- **AFTER:** Incremental delta per year + separate cumulative total columns

### Change #32 — BU Impact: Per-BU Pool Utilization (Page 17)
- Side-by-side per-BU pool utilization with per-lever progress bars
- Engine: Pool ceilings distributed across BUs by baseline FTE share

### Change #33 — BU Impact: Configurable Location Mix (Page 17)
- Range sliders for onshore/nearshore/offshore target mix
- Auto-normalizing (sum to 100%), per-BU location table, linked initiatives

### Change #38 — Scenario Comparison Fix + Rebuild (Page 21)
- Quick Wins: timeline recalculated after staggered starts; multi-criteria filter with fallback
- Consistent data: all metrics from actual initiative subsets (no impossible combinations)
- Initiative drilldowns: expandable list per scenario card
- Custom Scenario Builder: checkbox grid, layer quick-selects, real-time preview

---

## Packet 1: Foundation Fixes (Changes #8, #9, #12, #13, #23, #34, #35)
**Date:** 2026-02-27  
**Type:** Engine + Frontend  

### Change #8 — Fix Friction/Health Scoring Engine
- **Escalation weight** increased from 0.15 → 0.20 (redistributed from AHT/CSAT)
- Added **GAP_AMPLIFIERS**: escalation gaps now amplified 1.8x so 22%+ escalation rates correctly produce red scores instead of lingering in amber
- New **`_compute_friction_score()`** function: composite friction score (0-100) weighted across escalation (40%), AHT excess (25%), FCR shortfall (20%), CSAT shortfall (15%)
- Each queue score now includes `hasFriction` flag and `frictionScore`
- Queue scores now include `intent` and `bu` fields for downstream filtering

### Change #9 — Park Friction Map (Page 5) 🚧
- Added Page 5 to `PARKED_PAGES` object
- Nav item styled with opacity 0.4 and 🚧 icon
- Engine code in `diagnostic.py` preserved (not deleted)

### Change #12 — Context-Aware Recommendation Engine
- **Complete rewrite** of `engines/recommendations.py`
- Added **7 page-specific scoring functions**:
  - `_score_for_benchmarking()`: ranks by benchmark gap closure
  - `_score_for_cost_analysis()`: ranks by cost impact (CPC, deflection, location arbitrage)
  - `_score_for_heatmap()`: ranks by intent-channel specificity of friction findings
  - `_score_for_gap_analysis()`: ranks by number of problem areas addressed
  - `_score_for_self_service()`: ranks by deflection potential
  - `_score_for_opportunity_buckets()`: ranks by FTE capture potential
  - `_score_for_executive_summary()`: balanced scoring across all 3 layers
- `PAGE_SCORERS` dispatch map routes each page to its scorer
- Verified: different pages now produce distinct top-5 initiative lists

### Change #13 — Fix Wasted Spend ($0.0M → $5.0M)
- Wasted spend now computed from **two components**:
  1. **CPC excess**: contacts × (actual CPC − benchmark CPC)
  2. **Friction waste**: AHT excess, excess escalation costs (2.5x multiplier), FCR-driven repeat contacts
- New fields in `costAnalysis`: `wastedCpcExcess`, `wastedFriction`, `frictionDetails`
- Dependency on #8 (friction scoring) resolved

### Change #23 — Diagnostic-Roadmap Alignment
- `get_recommendations()` now **only considers enabled initiatives**
- New **`get_initiative_linkage()`** function with page-specific logic:
  - `benchmarking`: maps ALL below-benchmark metrics to addressing initiatives (#2)
  - `cost_analysis`: maps ALL above-benchmark cost queue/channels to cost-reducers (#15)
  - `heatmap`/`gap_analysis`: maps red/amber queues to channel-specific initiatives (#7)
- New API route: `GET /api/initiative-linkage/<page_context>`
- Ensures diagnostic linkage tables match what's on the Initiative Roadmap

### Change #34 — Park Workforce Transition (Page 13/14) 🚧
- Added Page 13 to `PARKED_PAGES` object
- Nav item styled with opacity 0.4 and 🚧 icon
- Engine code in `workforce.py` preserved (not deleted)

### Change #35 — Sub-Intent Engine Downstream Wiring
- Sub-intent analysis data now stored in `STATE['subIntentAnalysis']`
- New `_enrich_sub_intents_for_downstream()` function adds:
  - Initiative mapping per sub-intent (matched by complexity + lever)
  - Feasibility scores for self-service page
  - Deflectable channel list for channel strategy page
- Enriched data exposed via `subIntentEnriched` field in demo object

### Infrastructure
- Version bumped to v12 in health check
- Import updated: `get_initiative_linkage` added to app.py

## Packet 2: Diagnostic Pages Overhaul (Changes #40, #1-3, #4-6, #7, #10, #11, #13-15)
**Date:** 2026-02-27  
**Type:** Frontend (templates/index.html)  

### Change #40 — Executive Summary Complete Rebuild (SCR Framework)
- Replaced 200+ lines of renderPage1() with consulting-grade SCR narrative
- (1) Client context banner: company/industry/volume
- (2) Diagnostic verdict: health score with benchmark gap badges, red/amber/green queue counts
- (3) Top 3 problem themes: grouped by metric (AHT/FCR/CSAT) not individual queues, with affected queues + monthly cost impact
- (4) Total addressable opportunity: savings headline, FTE reduction, wasted spend, workforce impact %
- (5) Recommended path: Top 10 initiatives balanced across all 3 layers with rationale column
- (6) Business case punchline: NPV/IRR/investment/CX revenue in dark card
- (7) Navigation bar: Diagnose→Size→Solve→Plan with health/savings/initiatives/IRR
- Removed: Before vs After comparison, FTE Waterfall chart, old Executive Recommendation card
- Kept: Year-over-Year projection table

### Changes #1, #2, #3 — Benchmarking Page
- #1: Moved Diagnostic → Initiative Linkage block to TOP of page
- #2: Linkage now includes ALL below-benchmark metrics (FCR was missing, now shows all 7 with >5% gap)
- #3: Removed redundant Performance Gap Summary horizontal bar chart

### Changes #4, #5, #6 — Maturity Assessment Page
- #4: Added editable maturity scoring questions per dimension with dropdown selectors
  - 2-3 questions per dimension with calibrated options
  - `_getMaturityQuestions()` and `handleMaturityAnswer()` helper functions
  - Score recalculates on answer change → stored as override → re-renders
- #5: Moved Maturity Progression Journey section to TOP of page
  - Shows Achieved/Current/Target/Future with dimension badges
- #6: Target maturity level selector
  - 4-button selector (Reactive/Managed/Optimised/Predictive)
  - Improvement Path recalculates based on selected target
  - Skip-level warning when target > current + 1

### Change #7 — Heatmap Context-Aware Linkage
- Added context-aware initiative linkage block (fetches from /api/recommendations/heatmap)
- Table shows: initiative name, layer badge, linked hotspot badges, impact
- Worst cells "So What" insight preserved above linkage

### Change #10 — Gap Analysis Full Rebuild
- New "Performance Gap Summary — All 7 Metrics" table at top
  - Shows current average vs benchmark, gap %, RAG badge, visual progress bar for each metric
- Mismatch table now shows ALL misaligned queues (was truncated to 12)
- Root Cause Analysis table now shows top 3 drivers per queue (was only primary)
  - Columns: Primary Driver, 2nd Driver, 3rd Driver, Root Cause Summary
  - All 7 metrics scored (added Escalation, CES to existing 5)
- Added context-aware initiative linkage via /api/recommendations/gap_analysis

### Changes #11, #13, #14, #15 — Cost Analysis Page
- #14: Moved Diagnostic → Initiative Linkage to TOP of page
- #15: New "Above-Benchmark Cost Queues — All Channels" table
  - Shows ALL queues exceeding their channel CPC benchmark
  - Includes: Your CPC, Benchmark, Gap %, Excess Cost, Volume
  - Summary: total excess spend across all above-benchmark queues
- #13: Fixed Wasted Spend calculation
  - Changed from total red queue cost → proportional waste: volume × cpc × (1 - health)
  - Label updated to "Wasted Spend (Proportional)"
- #11: Cost-context-specific recommendations via /api/recommendations/cost_analysis
  - Ranked by cost reduction impact, not generic

### Summary of Packet 2
| Change | Page | Status |
|--------|------|--------|
| #40 | Exec Summary | ✅ Complete |
| #1,#2,#3 | Benchmarking | ✅ Complete |
| #4,#5,#6 | Maturity | ✅ Complete |
| #7 | Heatmap | ✅ Complete |
| #10 | Gap Analysis | ✅ Complete |
| #11,#13,#14,#15 | Cost Analysis | ✅ Complete |

**Files Modified:** templates/index.html (all changes frontend)

## Packet 3: Size the Prize (Changes #16, #17, #18, #19, #20, #22, #21)
**Date:** 2026-03-01  
**Type:** Frontend (templates/index.html)  

### Changes #16, #17, #19, #20 — Opportunity Buckets UX Overhaul
- #20: **Methodology note moved to TOP** of page — collapsible `<details open>` explaining pool-based netting, safety caps, and CSAT methodology
- #17: **KPI Term Definitions** — inline legend below methodology explaining: FTE Pool Ceiling, FTE Captured, FTE Untapped, FTE Untapped Value, CX Revenue Opportunity with color-coded labels
- #16: **Pool Ceiling Explanation** — new section showing why pool ceiling differs from total headcount:
  - Visual bar: in-scope vs out-of-scope FTE count
  - Per-role breakdown: addressable vs protected FTE for each role (uses PER_ROLE_MAX_REDUCTION cap)
- #19: **Progress bar labels clarified** — all bars now show "X% captured — Y of Z FTE" format:
  - Mini bar chart overview: absolute values under each bucket
  - FTE bucket cards: "61% captured — 123 of 201 FTE"
  - CSAT bucket: "45% of CX opportunity captured — $1.2M of $2.7M/yr"

### Change #18 — Sub-Intent Opportunity Breakdown
- New "Sub-Intent Opportunity Detail" table at bottom of Opportunity Buckets page
- Sources data from `DEMO.subIntentEnriched` (wired in Packet 1, Change #35)
- Groups sub-intents by parent intent with columns: Vol Share, Complexity, Primary Lever, Deflectable?, Est. FTE Impact
- FTE impact estimated: sub-intent volume × automation potential / annual contacts per FTE
- Falls back gracefully if sub-intent data not available

### Change #22 — Self-Service Feasibility Sub-Intent Breakdown
- Intent-level feasibility table rows are now **expandable** — click to show sub-intent detail
- Each intent row shows "▸ N sub-intents" indicator when data available
- Expanded sub-intent rows show: individual complexity, feasibility score, deflection potential, volume share, monthly saving
- Sub-intent feasibility calculated with same formula as parent: (1 − complexity) × 80 + adjustment
- Sub-intent rows styled with indented "↳" prefix and light gray background
- Uses `<tbody>` toggle for clean show/hide without JavaScript framework

### Change #21 — Visual Differentiation of Recommendation Panels
Each diagnostic page now has a **distinct visual identity** for its linkage/recommendation block:
- **Benchmarking (Page 2):** Green gradient background, "Gap Closure" framing with 🔗 icon — already unique from v12 Packet 2
- **Heatmap (Page 4):** Red gradient (#FFF5F5→#FFEBEE), renamed "Hotspot Intervention Map"
- **Gap Analysis (Page 6):** Amber gradient (#FFFDE7→#FFF8E1), renamed "Root Cause → Remedy Map"
- **Cost Analysis (Page 7):** Green gradient (#E8F5E9→#F1F8E9), renamed "Cost Reduction Levers"
- **Self-Service (Page 8/9):** Yellow gradient "Automation Strategy Recommendation" — already unique

### Summary of Packet 3
| Change | Page | Status |
|--------|------|--------|
| #16 | Opp Buckets | ✅ Pool ceiling explanation |
| #17 | Opp Buckets | ✅ KPI term definitions |
| #18 | Opp Buckets | ✅ Sub-intent breakdown |
| #19 | Opp Buckets | ✅ Progress bar absolute labels |
| #20 | Opp Buckets | ✅ Methodology moved to top |
| #22 | Self-Service | ✅ Sub-intent feasibility rows |
| #21 | Global | ✅ Visual differentiation |

**Files Modified:** templates/index.html, changelog_v12.md

---

## Packet 4: Solve Section (7 Changes)

### Change #37 — Operating Model: INPUT Not Output (Critical)
- Complete page rebuild: Operating Model is now strategic INPUT driving downstream
- Location mix sliders (Onshore/Nearshore/Offshore) with real-time re-render
- Offshore auto-computes as remainder (100 - onshore - nearshore)
- Current vs Target side-by-side with blended cost comparison
- Cost-Effort Bridge: Current Cost → [FTE reduction + location arbitrage] → Target Cost
- Tiered Model editor with editable volume share per tier (Tier 0-3)
- Validation: warns if tier shares ≠ 100%
- Persistent state via `window._targetOpModel` — survives page switches
- Required Initiatives table: shows which Location Strategy initiatives needed
- "So What" box: combined savings (FTE + location arbitrage)

### Change #24 — Channel Strategy: Intent-Channel Pair Tiles
- KPI tiles now labeled as "intent-channel pairs" not raw channel counts
- New Action Breakdown section: visual cards per action (SCALE UP, INTRODUCE, etc.)
- Each card lists the specific intent→channel combinations in that category

### Change #25 — Channel Strategy: Intent-Channel Portfolio + Maintain Toggle
- Portfolio table converted from channel-level to intent-channel level
- Added CPC column per intent-channel pair
- INTRODUCE rows now show intent total volume (not dashes)
- MAINTAIN toggle checkbox (default: hidden) with count indicator
- Health column shows "new" for INTRODUCE rows

### Change #26 — Channel Strategy: Initiative-Driven Migration Flow
- Replaced static Sankey with intent-level migration flow table
- Flows derived from SCALE DOWN/RETIRE → SCALE UP/INTRODUCE pairings
- Shows per-intent migration: from channel, to channel, volume, annual saving
- Footer: "Driven by N enabled initiatives — toggle on Roadmap to update"
- Removes dependency on pre-computed `DEMO.channelSankey`

### Change #27 — Channel Strategy: Migration Risks + CX Gates + Dynamic Savings
- **Migration Risks:** reframed as concrete risk warnings per migration flow
  - Computes source FCR/CSAT; flags if below threshold
  - "Source FCR is 62% — migrating unresolved contacts risks repeat calls"
- **CX Safeguard Gates:** actual trigger logic with thresholds
  - CSAT < 65% or FCR < 60% blocks inbound migration to that channel
  - Table: channel, metric, current value, threshold, gate action
- **Dynamic Savings:** computed from enabled deflection initiatives
  - Annual migration saving, migrated volume, digital mix shift
  - Shows which initiative IDs drive the savings

### Change #28 — Implementation Gantt: Inline Editable
- Added Start Month and Duration columns with `<input type="number">` fields
- User edits trigger `window._ganttEdit()` → updates initiative data → re-renders
- Gantt bars, milestones, and Active Count row all update immediately
- Supports workshop-style live adjustment without backend round-trip

### Change #36 — Sub-Intent Analysis: Configurable + Initiative Mapping
- **Volume Share:** editable inline number input per sub-intent
- **Complexity:** editable dropdown (simple/moderate/complex)
- **Automation Potential:** editable inline number input
- All edits recalculate parent intent metrics in real-time
- **Deflectable Channel column:** inferred from automation potential
  - >70%: App/Self-Service, Chat, SMS/WhatsApp
  - >40%: Chat, SMS/WhatsApp
  - >20%: Chat only
- **Linked Initiatives column:** maps enabled initiatives to sub-intents by complexity
  - Simple → chatbot/self-service/IVR initiatives
  - Moderate → agent assist/knowledge/copilot
  - Complex → skill-based routing/escalation
- Layer-colored initiative badges

---

## Cumulative Progress: 34 of 40 Changes Delivered

| Packet | Scope | Changes | Status |
|--------|-------|---------|--------|
| 1 — Foundation | Systemic fixes, parks, engine overhauls | 13/13 | ✅ |
| 2 — Diagnostic | Exec Summary, Benchmark, Maturity, Heatmap, Gap, Cost | 14/14 | ✅ |
| 3 — Size the Prize | Opportunity Buckets, Self-Service, Visual differentiation | 7/7 | ✅ |
| 4 — Solve | Operating Model, Channel Strategy, Gantt, Sub-Intent | 7/7 | ✅ |
| 5 — Business Case | Impact Dashboard, BU Impact, Scenario Comparison | 0/6 | Pending |
| 6 — Polish | Login redesign | 0/1 | Pending |

# Navigator v11 — Changelog (P2-1 through P2-11 Complete)

## P2-1: Dimensional Engine (Engine + Frontend)

### Engine Changes
| File | Change | LOC delta |
|---|---|---|
| `data_loader.py` | Location cost matrix, queue dimension map, location/sourcing on queues+roles | +95 |
| `pools.py` | Per-BU pool ceilings, BU-weighted costs, BU consumption tracking | +85 |
| `gross.py` | BU-filtered queue matching, targetBUs support | +15 |
| `waterfall.py` | Per-BU FTE/saving tracking, buSummary output, location savings per BU | +40 |
| `parameters.xlsx` | 2 new sheets (Location Cost Matrix, Queue Dimension Map) | data |

### Frontend Changes
| File | Change |
|---|---|
| `index.html` | Page 17: BU Impact Views (BU cards, yearly trajectory table, pool utilization by BU, location mix, BU cost comparison) |
| `index.html` | BU filter dropdown in topbar |
| `app.py` | Dimensional fields passed to frontend (locations, sourcingTypes, locationCostMatrix, buSummary) |

### New Output: `buSummary`
- Consumer: 1,128 FTE baseline → -255 FTE Year 3, $14.8M/yr saving
- Enterprise: 87 FTE baseline → -32 FTE Year 3, $1.9M/yr saving

## P2-2: Workforce Transition (Engine + Frontend)

### Engine Changes (`workforce.py` — full rewrite, +200 LOC)
- Location-aware severance costs (Onshore 100%, Nearshore 60%, Offshore 35% multiplier)
- Location-aware reskill costs (Onshore 100%, Nearshore 70%, Offshore 50%)
- Sourcing-aware transitions: Outsourced/Managed FTE use contract adjustment (10% early termination) instead of severance/reskill
- Per-location attrition rates from cost matrix (Onshore 2.5%/mo, Nearshore 3.5%/mo, Offshore 5%/mo)
- Expanded reskill paths: all 10 roles covered (was 5), including QA Analyst, WFM Analyst, Trainer, Knowledge Manager, Reporting/Analytics
- New outputs: `byLocation`, `byBU`, `reskillDemand` aggregations

### Frontend Changes (`index.html` — Page 13 rebuilt)
- Un-parked page 13 (was 🚧 Parked)
- 5 KPI cards: Total FTE reduction, Natural attrition, Redeployed, Separated+Contract Adj, Transition cost
- Absorption indicator (green/amber based on months to absorb)
- Role impact table with Location + Sourcing columns
- Transition mechanism by year (attrition vs redeployed vs separated vs contract adj)
- Attrition vs reduction canvas chart
- Location breakdown table
- BU impact breakdown table
- Reskill pathways: demand-based table when reskilling needed, full pathway catalog otherwise

### App.py Changes
- New fields passed: `reskillDemand`, `workforceByLocation`, `workforceByBU`

## Regression Status
**ALL PASS ✅** — Every global financial metric matches v10 exactly:
- NPV: $26,573,933 | Saving: $33,619,457 | FTE: 287 | Investment: $5,220,055
- ROI: 409.1% | IRR: 123.5% | Payback: 3.4 months | 30 initiatives enabled

## P2-7: Risk Assessment Rebuild (Engine + Frontend)

### Engine Changes (`risk.py` — full rewrite, +180 LOC)
- **3-axis scoring**: Implementation Risk, CX Risk, Operational Risk (each 1–5 scale)
- **Implementation risk** factors: effort/complexity, channel integration breadth, timeline pressure, technology maturity (AI penalty)
- **CX risk** factors: adoption shortfall, deflection disruption, volume magnitude, intent complexity
- **Operational risk** factors: roles affected, FTE impact magnitude, multi-BU scope, location moves, dependency chains
- **Weighted overall**: 35% impl + 30% CX + 35% ops
- **Mitigation library**: Axis-specific mitigations (impl_high, cx_high, ops_high, etc.) with context-aware selection
- **Dependency map**: 5 dependency chains (e.g., AI05→AI01, AI08→AI01+AI02) with unmet dependency warnings
- **Risk by Layer**: Average risk per transformation layer (AI, OpModel, Location)
- **Risk by BU**: Per-BU risk exposure (count, avg risk, saving at risk, high-risk count)
- **4-tier rating**: critical (>4.0), high (>3.0), medium (>2.0), low (≤2.0)

### Frontend Changes (`index.html` — Page 12 rebuilt)
- Un-parked page 12 (was 🚧 Parked)
- 5 KPI cards: High/Critical count, Medium, Low, Portfolio avg, Saving at risk
- Layer risk cards with avg risk, initiative count, high-risk count per layer
- 3-axis risk register table (Impl / CX / Ops / Overall / Rating)
- Risk vs Impact scatter plot (quadrant: safe bets, monitor, low priority, mitigate)
- BU risk exposure cards
- Top mitigations panel (axis-specific, context-aware)
- Dependency map with blocked/unmet warnings
- "So What" recommendation box

### App.py Changes
- New fields passed: `riskByLayer`, `riskByBU`, `riskDependencies`
- Fixed risk merge to use `r.overallRisk` (was `r.overall`)

## P2-5: 3-Tier Role-Based Access Control

### Database Changes (`infrastructure/database.py`)
- 3 default users: admin/admin123 (admin), supervisor/super123 (supervisor), analyst/analyst123 (analyst)
- Role hierarchy: admin > supervisor > analyst

### Auth Changes (`infrastructure/auth.py`)
- `ROLE_HIERARCHY` dict with numeric levels (admin=3, supervisor=2, analyst=1)
- `require_role(min_role)` decorator supports hierarchical access (admin can access supervisor endpoints)

### API Restrictions (`app.py`)
- **Admin only:** file upload, clear uploads, data source switching
- **Supervisor+:** initiative toggle/update, override, batch operations, recalculate
- **Analyst:** read-only (all GET endpoints, export)

### Frontend (`index.html`)
- `window.USER_ROLE` global variable set from auth response
- `applyRoleRestrictions()` hides elements with `.role-supervisor` or `.role-admin` classes
- Initiative action buttons tagged with `role-supervisor` class
- Data Management nav tagged with `role-admin` badge
- Restrictions applied after each page render

## P2-6: Excel Export with Dimensional Breakdown

### Export Enhancements (`app.py`)
- **Executive Summary** sheet: added ROI, Payback, FTE Reduction, Enabled Initiatives
- **Initiatives** sheet: added Impl Risk, CX Risk, Ops Risk, Overall Risk, Rating columns
- **New: BU Impact** sheet: per-BU yearly FTE reduction and saving
- **New: Risk Register** sheet: full 3-axis risk scores with mitigations
- **New: Workforce Transition** sheet: per-role transitions with location/sourcing
- **New: Location Cost Matrix** sheet: location × sourcing cost parameters

## P2-9: Sub-Intent Analysis Engine + Page 18

### Engine (`engines/diagnostic.py`, +100 LOC)
- `SUB_INTENT_MAP`: 6 intent categories decomposed into 4-5 sub-intents each
- `DEFAULT_SUB_INTENTS`: fallback for unmapped intents (simple/standard/complex/exceptions)
- Each sub-intent has: volumeShare, complexity, automationPotential, estimatedAHT
- `build_sub_intent_analysis()`: aggregates volume by intent, computes per-sub-intent metrics
- Returns: intent objects with sub-intent breakdown, totalDeflectable volume

### Frontend (Page 18 — Sub-Intent Analysis)
- 4 KPI cards: categories, sub-intents, avg automation potential, annual deflectable volume
- Per-intent expandable cards with sub-intent breakdown table
- Automation potential bars with color coding (green >60%, amber 35-60%, red <35%)
- "So What" box with high/low automation intent recommendations

## P2-4: Queue-Level Analysis — Page 19

### Frontend (Page 19 — Queue Analysis)
- 5 KPI cards: active queues, monthly volume, weighted avg AHT/FCR, locations
- Full queue performance table: BU, intent, channel, location, sourcing, volume, share, AHT, FCR, CSAT, complexity
- Color-coded AHT/FCR with thresholds
- Channel × BU volume cross-tabulation matrix
- Location volume distribution cards

## P2-8: Operating Model Recommendations — Page 20

### Frontend (Page 20 — Operating Model)
- 4 KPI cards: future FTE, current/optimal blended cost, location arbitrage opportunity
- Current vs Recommended location mix comparison (bar visualizations)
- Optimal mix: shift onshore → nearshore/offshore (40/35/25 target)
- 4-tier operating model (Tier 0 Self-Service → Tier 3 Expert) with FTE allocation, locations, key roles
- CoE (Centre of Excellence) recommendation
- Location arbitrage savings calculation

## P2-11: Scenario Comparison — Page 21

### Frontend (Page 21 — Scenario Comparison)
- 4 pre-built scenarios: Current Plan, AI-First, Quick Wins Only, Conservative
- Each scenario card: initiatives count, FTE reduction, saving, NPV, investment, ROI, payback, risk
- Side-by-side comparison table
- Auto-recommendation: highest NPV, lowest risk, fastest payback
- Scenario metrics derived from real initiative data and waterfall output

## P2-10: Initiative Filter Fix

### Frontend Fix
- Priority matrix redraws after layer filter changes
- `setTimeout(() => drawPriorityMatrix(), 100)` after savedLayer re-application

## What's NOT Yet Built
- All P2 items (P2-1 through P2-11) are now complete
- Page 15 (Overrides) remains parked — replaced by Data Management panel

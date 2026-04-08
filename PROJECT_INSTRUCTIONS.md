# Rent vs. Buy Financial Simulation — Project Instructions

## Priority #1: Code Quality

Every decision filters through code quality first, in order:

1. **Accuracy** — Every calculation traceable and correct. No silent assumptions.
2. **Simplicity** — Fewer lines, fewer parameters, fewer branches. Complexity is a bug.
3. **Redundancy elimination** — One source of truth per value. No duplicated state.
4. **Explainability** — Every default, formula, and assumption documented inline or in research docs.
5. **Testability** — The simulation engine is a pure function: same inputs → same outputs.

---

## What This Project Is

An interactive single-file HTML application that compares renting vs. buying a home across 16 countries and 54 metros. Built for Allan Comeau's family discussion (siblings in mid-to-late twenties evaluating homeownership), designed to be generalizable. Built entirely with Claude Opus 4.6.

**Live at:** https://allancomeau.github.io/rent-vs-buy/

---

## Current State: v3.8 — Layout & Theme Integrity

### Architecture

**Single HTML file** (`index.html`, ~107KB, ~1155 lines) containing:
- CDN script tags (React 18, ReactDOM, react-is, prop-types, Recharts, Babel Standalone)
- One `<script type="text/babel">` block with all application code
- Inter font from Google Fonts
- Slider + theme CSS in `<style>` block with CSS custom properties

**Critical CDN dependencies (all from unpkg):**
- react@18.2.0
- react-dom@18.2.0
- react-is@18.2.0 (required by Recharts)
- prop-types@15.8.1
- recharts@2.15.3
- @babel/standalone@7.26.10

**React hooks destructured on line ~54:**
```js
const { useState, useMemo, useCallback, useEffect } = React;
```
Any new hook must be added here or Babel will white-screen.

**Verification checklist after every build:**
- Braces balanced
- Parens balanced
- Brackets balanced
- Backticks even count
- Scripts: 8/8
- No `import {` or `export default` statements
- No `</script>` inside the Babel script block
- COUNTRIES defined exactly once (no duplication)
- All key functions present: backsolveLoan, simulate, buildParams, RentVsBuyV3, runSensitivity

### Monthly-Payment-First Architecture

Primary inputs: Monthly Payment ($) + Down Payment ($). Home price is DERIVED:
```
L = M × [(1+r)^n − 1] / [r × (1+r)^n]
homePrice = L + downPayment
```
Same payment at different mortgage rates → different derived home prices.

### Page Layout (top to bottom)

1. **Header** — Centered. "Should I Rent or Buy?" as h1. Tutorial toggle (iOS switch), theme selector (colored pill dots), Share Link button.
2. **Open Source Banner** — Dismissible per session. Mentions Claude Opus 4.6, links to GitHub.
3. **Tutorial Nudge** — One-line hint (visible when tutorial is off). FAQ link scrolls to guide via scrollIntoView.
4. **Guide** — 4 tabs: How It Works, Methodology, Sources & Limitations, FAQ. Open by default.
5. **Your Situation** — Personal inputs shared across all panels.
6. **Three Panels** — Location (locked, blue), Scenario (preset-driven, purple when active), Custom (shows "edited" when changed from preset).
7. **Verdict Callout** — Between panels and chart. Verdict + confidence explanations + conditional insights.
8. **Chart** — 6 types in dropdown (wealth default, tornado 2nd). Collapsible auto-description. Expandable data table.
9. **Footer** — Version, LinkedIn, GitHub, disclaimer.

### Theme System (10 themes)

Module-level `let T` updated on each render. `buildS(T)` recomputes styles. CSS custom properties for pseudo-elements.

**Core:** Light (default), Dark, Warm, Slate.
**Extended:** Midnight, Ocean, Ember.
**Additional:** Nord, Sand, Moss.

Active pill uses `v.card` background + `v.text1` text (fixes Midnight visibility). Theme persists via URL sharing.

### Three-Panel Layout

| Panel | Purpose | Visual State |
|-------|---------|--------------|
| **Location** | Real sourced data, locked | Always blue accent |
| **Scenario** | Preset market conditions (12 options) | Gray default, purple active |
| **Custom** | Fully editable sandbox | Shows "Custom (edited)" when overrides don't match any preset |

Custom panel uses JSON.stringify preset comparison — minor per-render cost, worthwhile for UX.

### 6 Chart Types (reordered for user flow)

1. **Total Wealth** (default) — Buyer vs renter total wealth
2. **Sensitivity Tornado** — Bars sorted by impact, star marks top variable
3. **Advantage** — All 4 scenario lines overlaid
4. **Annual Cost** — Buyer vs renter with payoff marker
5. **Home Equity** — Value, balance, equity
6. **Invested Savings** — Portfolio comparison

### Verdict Callout

Positioned between panels and chart. Includes:
- Verdict sentence with theme-aware background
- Home price derivation with FX conversion
- Confidence indicators with inline explanations (🟢🟡🔴)
- Conditional insights (⏱💡📌⚠)
- Large-advantage explainer (>$200K at 100% discipline)
- Disclaimer in red

---

## Data Coverage

### 16 Countries
US, Canada, UK, Australia, Germany, Ireland, New Zealand, France, Spain, Netherlands, Italy, Sweden, Switzerland, Japan, Singapore, South Korea.

### Tax Treatment Buckets
- **Bucket A (marginalTax = 0):** CA, UK, AU, DE, IE, NZ, FR, ES, IT, JP, SG, KR
- **Bucket B (marginalTax > 0):** NL (37%), SE (30%), CH (complex, set to 0)
- **US:** Full deduction with excess itemization method
- **Structural disclaimers:** JP (building depreciation), SG (HDB), KR (jeonse)

### 54 Metros
24 US, 5 CA, 3 UK, 4 AU, 2 DE, 1 IE, 2 NZ, 2 FR, 2 ES, 1 NL, 2 IT, 1 SE, 1 CH, 2 JP, 1 SG, 1 KR.

---

## Simulation Engine (Pure Function)

### `simulate(params)` — deterministic, no side effects
Month-precise amortization, excess itemization tax benefit (post-TCJA), symmetric surplus investing, renter discipline toggle.

### `backsolveLoan(monthlyPayment, annualRate, termYears)` — loan amount
Standard amortization inversion.

### `buildParams(country, metro, personal, rateOverride)` — full parameter set
Merges country defaults, metro overrides, personal inputs into flat parameter object.

### Engine Bug Fixes (v3.0, from math audit)
- Bug 1: Final mortgage payment uses `mi + mp` not full `monthlyPmt`
- Bug 2: Selling costs reduce capital gain per IRS Pub 523

### Performance
780 iterations/sim × 4 scenarios + 12 sensitivity = ~12,000 iterations. Microseconds.

---

## Research Files (7 documents)

| File | Purpose |
|------|---------|
| `rent_vs_buy_research_report.md` | Academic foundations (Poterba, Shiller, Jordà, Himmelberg, Ben Felix) |
| `rent_vs_buy_research_report_v2.md` | Volatility drag, arithmetic vs geometric means |
| `defaults_rationale.md` | Every default value sourced (16 countries, 475 lines) |
| `what_model_doesnt_capture.md` | Known limitations with directional bias analysis |
| `rent-vs-buy-sim-audit.md` | Formula-by-formula math verification, 23 items |
| `UI_and_UX_Research_Report.md` | Design tokens, typography, layout patterns |
| `FAQ.md` | 115 research-backed questions across 11 sections |

---

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Investment return = 10% (geometric/CAGR) | 12% arithmetic overstates by 50-70%. g ≈ a − σ²/2. |
| International tax via parameter values | Engine unchanged. Country differences = parameter values. |
| Single HTML file deployment | No build tools, no backend, works anywhere. |
| Engine NEVER modified | All features call simulate() with different inputs. |
| No cross-country comparison | Currency/tax incompatibility. Within-country dual-metro only. |
| Snapshot model, not transition | Each year = independent "if I liquidated now." |
| Wealth chart as default | Non-alarming first impression vs -$2M advantage chart. |
| Tornado chart 2nd | Most important analytical tool — promoted for discoverability. |
| 10 curated themes, no raw pickers | Protects readability. |
| Verdict between panels and chart | Configure → result → visualize flow. |

---

## Build Workflow

### Development (in Claude)
1. Start from Allan's most recent uploaded `index.html` (single source of truth)
2. Apply edits via `str_replace` tool
3. Run verification script (balanced syntax, no duplicates, all functions present)
4. Copy to outputs, present file for download

### Deployment (Allan)
1. Download from Claude outputs
2. Test locally in browser (no white screen, features work)
3. Upload to GitHub repo as `index.html`
4. GitHub Pages auto-deploys

### Critical Build Rules
- Always start from Allan's latest upload — never from diverged local copy
- Never serve without verification script
- New React hooks must be added to destructuring on line ~54
- The assembled `index.html` is the single source of truth

---

## Versioning

Format: `vX.Y` or `vX.Y.Z`. Version in footer.

| Version | Title | Key Changes |
|---------|-------|-------------|
| v3.0 | Engine & Architecture | Payment-first, 16 countries, 54 metros, 3 panels, 6 charts, sensitivity |
| v3.5 | UX Polish | 7 themes, table merge, chart labels, slider gradient, mobile CSS |
| v3.6 | Header & FAQ | Centered header, FAQ tab, tutorial nudge |
| v3.7 | UX & Communication | iOS toggle, banner, chart auto-description, emoji cleanup, FAQ.md |
| v3.7.5 | Theme Bug Fixes | Midnight pill fix, 10 themes, wealth chart default |
| v3.8 | Layout & Theme Integrity | Layout reorder, verdict explanations, Custom label, CSS audit, tornado 2nd |

---

## Pending Items

### v3.9 — Panel Polish
- [ ] Scenario/Custom panel CSS color states
- [ ] Remaining UX friction

### v4.0 — Mobile
- [ ] Responsive CSS (panels, chart, touch targets, axis labels)

### v4.1+ — Enhanced Interactivity
- [ ] FAQ accordion
- [ ] Universal tooltip toggle
- [ ] Tutorial glow/highlighting

### Future (v5+)
- [ ] Refinance toggle
- [ ] HELOC toggle
- [ ] RNG / volatility simulation
- [ ] PMI modeling

---

## Design Philosophy

1. **Quality over features.** A correct model with 10 inputs beats a buggy model with 30.
2. **Accuracy over advocacy.** The model reveals which path wins under which assumptions. It does not lean.
3. **Explainability over aesthetics.** Every result traces to inputs.
4. **Simplicity over completeness.** If a feature can't be implemented correctly, document it as a text note.
5. **Customizability as credibility.** Users control all assumptions and see how results change.
6. **Open source as trust.** Every formula, default, and data source is documented and accessible.|-------|---------|-----------------|--------------|
| **Location** | Real sourced data, locked | Locked to country/metro reference data | Always blue |
| **Scenario** | Preset market conditions | Editable, initialized from 12 presets | Gray when default, purple when active |
| **Custom** | Fully editable sandbox | Editable, "Load preset" option | Gray when default, dark when active |

Personal variables (payment, down, term, sell year, discipline) are shared across all three panels.

### Location Panel Features

- Country dropdown in panel header (16 countries)
- 🔗 Link toggle: linked = A shows national avg, B shows metro. Unlinked = any two metros.
- Dual verdict boxes with break-even data

### 12 Presets

Match reference, Low rate era, High rate era, Conservative, Hot market, Stagnant, Inflation surge, Rate cuts, Stock underperformance, Housing correction, Best case buy, Best case rent.

### 6 Chart Types (dropdown selector)

1. **Advantage** — All 4 scenario lines, hold year marker
2. **Wealth Paths** — Buyer vs renter total wealth (custom)
3. **Annual Cost** — Buyer net cost vs renter cost, sell year + payoff markers
4. **Equity Buildup** — Home value, mortgage balance, equity
5. **Portfolio Comparison** — Renter's seeded portfolio vs buyer's post-payoff portfolio
6. **Sensitivity Tornado** — Horizontal bars sorted by impact

### Chart-Table Sync

Bottom section has "Synced to chart" / "Full data" toggle. When synced, table columns match the selected chart type. Tornado syncs to the sensitivity table. Full data shows the complete 15-column audit table.

### Bottom Line (Combined Callout)

Single box replacing separate "Your Numbers" and "Dynamic Insight" sections. Shows:
- Verdict sentence in plain English
- Home price derivation with USD conversion for non-US countries
- Rich conditional insights:
  - 🔴 Highly assumption-dependent (top 2 variables exceed advantage)
  - 🟡 Fragile (top 1 exceeds advantage)
  - 🟢 Robust (no single swing exceeds advantage)
  - ⏱ Breakeven proximity warning
  - 💡 100% discipline reminder
  - 📌 Negative spread alert
  - ⚠ High appreciation warning

### Click Tooltips

Blue **?** icon on every input label. Click to show dark tooltip with research-cited description. Mouse-leave dismisses. TIPS object contains entries for all inputs.

### Sliders

Range sliders on all market inputs (Scenario/Custom panels) and personal inputs (Term, Sell Year, Sim Length, Discipline). Slider range = historical bounds or sensible universal bounds. Out-of-bounds indicator: amber input border + "Outside typical range (X–Y)" when value exceeds slider bounds.

### Currency Conversion

Hardcoded FX rates (Apr 2026 est.) with editable override input. Shows "≈ $X USD" next to amounts for non-USD countries. CUR_NAMES maps codes to full names (no "C$" — spelled out as "Canadian dollars"). Reset to default button. Disclaimer about approximate rates.

### Guide (Open by Default)

Three tabs: How It Works, Methodology, Sources & Limitations. Links to 4 research documents on GitHub. Full academic citations.

### URL State Sharing

"📋 Copy Shareable Link" button with subtitle explanation. Encodes all state in base64 URL hash.

---

## Data Coverage

### 16 Countries

US, Canada, UK, Australia, Germany, Ireland, New Zealand, France, Spain, Netherlands, Italy, Sweden, Switzerland, Japan, Singapore, South Korea.

### Tax Treatment Buckets

- **Bucket A (marginalTax = 0):** CA, UK, AU, DE, IE, NZ, FR, ES, IT, JP, SG, KR — no mortgage interest deduction
- **Bucket B (marginalTax > 0 with ⚠️):** NL (37%), SE (30%), CH (complex, set to 0)
- **US:** Full deduction with excess itemization method
- **Structural disclaimers:** JP (building depreciation), SG (HDB), KR (jeonse)

### 54 Metros

24 US, 5 CA, 3 UK, 4 AU, 2 DE, 1 IE, 2 NZ, 2 FR, 2 ES, 1 NL, 2 IT, 1 SE, 1 CH, 2 JP, 1 SG, 1 KR.

Per-metro data: appreciation (5yr/10yr CAGR), rent growth, effective property tax rate, insurance rate. All sourced.

---

## Simulation Engine (Pure Function)

### `simulate(params)` → deterministic, no side effects

- Month-precise amortization, aggregated annually
- Excess itemization tax benefit (post-TCJA correct)
- Symmetric surplus investing (both buyer AND renter invest surplus)
- Renter discipline toggle (Bernstein-Koudijs behavioral adjustment)
- Returns: full year-by-year data array + summary metrics (breakeven, homePrice, unrecoverable costs)

### `backsolveLoan(monthlyPayment, annualRate, termYears)` → loan amount

Standard amortization inversion.

### `buildParams(country, metro, personal, rateOverride)` → full parameter set

Merges country defaults, metro overrides, personal inputs, and rate overrides into the flat parameter object that simulate() expects. Derives homePrice from monthly payment.

### Performance

65 years × 12 months = 780 iterations per simulation × 4 scenarios + 12 sensitivity runs = ~12,000 iterations. Runs in microseconds.

---

## Research Files

| File | Purpose | Status |
|------|---------|--------|
| `rent_vs_buy_research_report.md` | Academic foundations (Poterba, Shiller, Jordà, Himmelberg, Ben Felix) | Complete |
| `rent_vs_buy_research_report_v2.md` | Volatility drag, arithmetic vs geometric means | Complete |
| `defaults_rationale.md` | Every default value sourced (16 countries, 475 lines) | Updated for v3 |
| `what_model_doesnt_capture.md` | Known limitations with directional bias analysis | Updated for v3 |
| `UI_and_UX_Research_Report` | Design tokens, typography, layout patterns | Reference |

---

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Investment return = 10% (geometric/CAGR) | 12% arithmetic overstates by 50-70%. Formula: g ≈ a − σ²/2. |
| International tax via parameter values | Engine unchanged. Country differences = parameter values, not conditional logic. |
| Single HTML file deployment | No build tools, no backend, works anywhere. CDN for React/Recharts. |
| Engine NEVER modified | All features call simulate() with different inputs or display existing output differently. |
| No cross-country comparison | Currency/tax incompatibility makes chart lines non-comparable. Within-country dual-metro only. |
| No live FX refresh | Static HTML can't call APIs. Hardcoded rates with editable override. |
| Snapshot model, not transition | Each year = independent "if I liquidated now." No sell-then-rent transition. |
| ScenarioPanel shared component | One component, two instances. Fewer lines, fewer divergence bugs. |

---

## Build Workflow

### Development (in Claude)
1. Edit `ui_new.jsx` (or `core.jsx` if engine changes needed — rare)
2. Assemble: `cat header.html core.jsx ui_new.jsx footer > index.html`
3. Verify: balanced braces/parens/brackets, all functions present, no imports/exports
4. Copy to outputs: `cp index.html /mnt/user-data/outputs/`
5. Present file for download

### Deployment (Allan)
1. Download `index.html` from Claude outputs
2. Go to GitHub repo `rent-vs-buy`
3. Replace `index.html` (edit → paste → commit, or delete + re-upload)
4. GitHub Pages auto-deploys

### Important: str_replace edits
When making targeted edits to `index.html` directly (via str_replace), the changes are NOT reflected in the source files (`core.jsx`, `ui_new.jsx`). After targeted edits, either:
- Apply the same edits to the source files, or
- Regenerate the source files from the assembled HTML (extract the relevant sections)

The assembled `index.html` is the single source of truth for the deployed app.

---

## Pending Items

### Near-term
- [ ] Tutorial mode (togglable at top next to share link)
- [ ] Aesthetic UI/UX polish pass
- [ ] Mobile/responsive design (60%+ of calculator traffic is mobile)
- [ ] Math verification research document
- [ ] Bug research document

### Future (v4+)
- [ ] Refinance toggle (on/off with year + new rate)
- [ ] HELOC toggle (on/off with year, % equity, rate, term)
- [ ] RNG / volatility simulation (fixed-seed approach, historical σ)
- [ ] PMI modeling (below 20% down; OBBBA 2026 makes PMI deductible)

---

## Design Philosophy

1. **Quality over features.** A correct model with 10 inputs beats a buggy model with 30.
2. **Accuracy over advocacy.** The model reveals which path wins under which assumptions. It does not lean.
3. **Explainability over aesthetics.** Every result traces to inputs.
4. **Simplicity over completeness.** If a feature can't be implemented without fragile special cases, it's documented as a text note.
5. **Customizability as credibility.** Users control all assumptions and see how results change.

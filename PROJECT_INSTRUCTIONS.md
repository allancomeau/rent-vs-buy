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

## Current State: v3.9 — Panel Polish

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
| v3.9 | Panel Polish | Guide closed default, home price callout, break-even clarity, full theme CSS audit |

---

## Pending Items

### v4.0 — Mobile
- [ ] Responsive CSS (panels, chart, touch targets, axis labels)
- [ ] 60%+ of calculator traffic is mobile — this is the big one

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
6. **Open source as trust.** Every formula, default, and data source is documented and accessible.

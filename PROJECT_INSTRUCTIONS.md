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

An interactive single-file HTML application that compares renting vs. buying a home across 16 countries and 54 metros. Built for Allan Comeau's family discussion (siblings in mid-to-late twenties evaluating homeownership), designed to be generalizable. Built entirely with Claude Opus 4.6 and 4.7.

**Live at:** https://allancomeau.github.io/rent-vs-buy/

---

## Current State: v3.9.99.10 — Small UI Polish

### Architecture

**Single HTML file** (`index.html`, ~120KB, ~1340 lines) containing:
- CDN script tags (React 18, ReactDOM, react-is, prop-types, Recharts, Babel Standalone)
- One `<script type="text/babel">` block with all application code
- Inter font from Google Fonts
- Slider + theme CSS in `<style>` block with CSS custom properties
- SEO meta block: OG tags, Twitter card, JSON-LD WebApplication schema, theme-color, favicon + apple-touch-icon

**Critical CDN dependencies (all from unpkg):**
- react@18.2.0
- react-dom@18.2.0
- react-is@18.2.0 (required by Recharts)
- prop-types@15.8.1
- recharts@2.15.3
- @babel/standalone@7.26.10

**React hooks destructured on line ~87:**
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
- `let T = THEMES.light` and `let S = buildS(T)` present between theme definitions and shared components (currently ~lines 689-690)
- All key functions present: backsolveLoan, simulate, buildParams, RentVsBuyV3, runSensitivity

### Monthly-Payment-First Architecture

Primary inputs: Monthly P&I + Down Payment. Home price is DERIVED:
```
L = M × [(1+r)^n − 1] / [r × (1+r)^n]
homePrice = L + downPayment
```
Same payment at different mortgage rates → different derived home prices.

**Budget split:** Rent Payment and Mortgage P&I are separate inputs with a 🔗 link toggle. Linked by default (apples-to-apples comparison); when unlinked, a >10% delta triggers an amber warning. The engine's `startRent` parameter now passes through from `personal` rather than being computed inside `simulate()`, allowing rent to be set independently from P&I.

### Page Layout (top to bottom)

1. **Header** — Centered. Fluid h1 (`clamp(24px, 5vw, 32px)`) reads "Rent vs Buy". Themes on their own centered row, tutorial toggle on a separate row below.
2. **Guide** — Two-line centered summary ("Guide, FAQ & Methodology" / "Open source · GitHub"). Closed by default. 4 tabs: How It Works, Methodology, Sources & Limitations, FAQ. `ref={guideRef}` for scroll targeting.
3. **Your Situation** — Personal inputs + financial snapshot + Location all inside one unified widget:
   - **Personal inputs:** Rent Payment + Mortgage P&I (linked by default), Savings / Down Payment, Mortgage Term, Holding Period, Investment Discipline
   - **Financial snapshot callout:** derived home price + country median, plus collapsible details (PMI threshold, income check at 28% front-end ratio, round-trip transaction costs)
   - **Location section** (below a 2px border-top): centered "LOCATION · [Country]" header with `·` separator, two metro dropdowns with inline link toggle (greyed-when-linked), per-location verdict boxes
4. **Your Assumptions + What-If panels** (two-column flex layout below Your Situation):
   - **Your Assumptions** — wide primary interactive panel (`flex: 2 1 320px`). Accent blue by default; purple with preset name in title ("Your Assumptions (Low Rate Era)") when a preset is loaded. Share button embedded in panel header.
   - **What-If** — narrow right column (`flex: 1 1 200px`), hidden by default. Dashed-border "+ Add What-If" placeholder expands into the full panel. Purple accent. ✕ close button returns to placeholder.
5. **Verdict card** — Winner headline centered at top (large), winner/loser details below, linked disclaimer. Clickable to expand KPI row: Break-even · Appr · Tax · Spread.
6. **Confidence & Warnings card** — Separate theme-aware widget below the verdict (green when robust, amber when fragile or 1 warning, red when highly dependent or 2+ warnings). Confidence gated so strong verdict (≥15% of home value) caps at 🟢. Hosts tax notes, input warnings, large-advantage explainer.
7. **FX converter** — Own minimal card, only renders for non-USD countries.
8. **Chart** — Dropdown IS the title (16px, centered, accent-bordered). 4 types: Net Worth (default), Sensitivity, Annual Cost, Equity & Investments. Sim Length lives here alongside the chart. Collapsible auto-description. Expandable year-by-year data table.
9. **Footer** — Version, LinkedIn, GitHub. Three-line legal disclaimer (independent project, employer separation, professional referral). Link to full legal disclaimers on GitHub.

### Theme System (8 themes)

Module-level `let T` updated on each render. `S = useMemo(()=>buildS(T),[theme])`. CSS custom properties for pseudo-element styling. `THEME_FONT` and `THEME_MONO` constants deduplicate font stacks across all themes.

**Light:** Light (default), Warm, Aqua, Citrus.
**Dark:** Dark, Ocean, Ember, Moss.

Theme selector shows single separator between light and dark groups. Active pill uses `v.card` background + `v.text1` text. Theme persists via URL sharing.

### Panel Architecture

| Panel | Purpose | Visual State |
|-------|---------|--------------|
| **Location** (merged into Your Situation) | Real sourced data, locked per-country | Muted neutral reference |
| **Your Assumptions** | Primary interactive panel — the user's scenario | Accent blue by default; purple with preset name when preset is active; "(edited)" when manually changed |
| **What-If** | Optional preset market conditions (12 options) | Hidden by default; dashed placeholder until expanded; purple when open |

Your Assumptions uses key-by-key preset comparison via `isEdited` rather than `JSON.stringify`. Preset tracking clears immediately on any input change (no continuous re-comparison).

### 4 Chart Types

1. **Net Worth** (default) — 8 lines: Your Assumptions buyer/renter (solid, ★), What-If buyer/renter when shown (purple, dashed for renter), locA/locB buyer/renter (dashed). Add What-If for preset comparison.
2. **Sensitivity Chart** (2nd, promoted) — Bidirectional bars showing resulting verdict at ±delta. Color = winner (blue = buying, green = renting) independently per side. ±delta prominent. Styled hover tooltips.
3. **Annual Cost** — Buyer vs renter annual housing costs with payoff marker.
4. **Equity & Investments** — Combined home equity (value, balance, equity) + investment portfolios (buyer and renter).

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
Month-precise amortization, excess itemization tax benefit (post-TCJA), symmetric surplus investing, investment discipline toggle (applies to both buyer and renter surplus). Reads `startRent` from params (passed through from `personal.rentBudget`) rather than computing internally.

### `backsolveLoan(monthlyPayment, annualRate, termYears)` — loan amount
Standard amortization inversion.

### `buildParams(country, metro, personal, rateOverride)` — full parameter set
Merges country defaults, metro overrides, personal inputs into flat parameter object. Forwards `startRent` from `personal.rentBudget` to support the Rent/P&I split.

### Engine Bug Fixes (v3.0, from math audit)
- Bug 1: Final mortgage payment uses `mi + mp` not full `monthlyPmt`
- Bug 2: Selling costs reduce capital gain per IRS Pub 523

### Performance
780 iterations/sim × 4 scenarios + 12 sensitivity = ~12,000 iterations. Microseconds.

---

## Research Files

| File | Purpose |
|------|---------|
| `rent_vs_buy_research_report.md` | Academic foundations (Poterba, Shiller, Jordà, Himmelberg, Ben Felix), volatility drag, arithmetic vs geometric means |
| `defaults_rationale.md` | Every default value sourced (16 countries, 475 lines) |
| `what_model_doesnt_capture.md` | Known limitations with directional bias analysis |
| `rent-vs-buy-sim-audit.md` | Formula-by-formula math verification, 23 items |
| `UI_and_UX_Research_Report.md` | Design tokens, typography, layout patterns |
| `FAQ.md` | 115 research-backed questions across 11 sections |
| `legal_disclaimers.md` | Six-component legal disclaimer + privacy + warranty |
| `target_audience_research.md` | First-time buyer demographics, PMI, down payment data |
| `Modern_CSS_25-26_Research.md` | CSS architecture, fluid design, container queries |

---

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Investment return = 10% (geometric/CAGR) | 12% arithmetic overstates by 50-70%. g ≈ a − σ²/2. |
| Investment Discipline default = 65% | Middle of Bernstein-Koudijs (QJE 2024) 50–80% realistic range. Honest default; users see realistic numbers on load rather than near-perfect. |
| International tax via parameter values | Engine unchanged. Country differences = parameter values. |
| Single HTML file deployment | No build tools, no backend, works anywhere. |
| Engine NEVER modified for cosmetic changes | All features call simulate() with different inputs. |
| No cross-country comparison | Currency/tax incompatibility. Within-country dual-metro only. |
| Snapshot model, not transition | Each year = independent "if I liquidated now." |
| Net Worth chart as default | Non-alarming first impression vs -$2M advantage chart. |
| Sensitivity chart 2nd | Most important analytical tool — promoted for discoverability. |
| 8 curated themes, no raw pickers | Protects readability. 4 light + 4 dark. |
| Verdict + Confidence as two separate widgets | Prevents contradiction (e.g., "Strong Rent" + 🔴). Verdict reports outcome; Confidence reports robustness, gated by verdict strength. |
| What-If hidden by default | Progressive disclosure. Two-panel default (Location + Your Assumptions) reduces cognitive load for first-time users. |
| Location merged into Your Situation | Single unified widget. Country is scoping metadata, not a comparison target. |
| Rent Payment + Mortgage P&I split with link toggle | Addresses "apples-to-apples" concern without removing flexibility for users who have a real landlord rent to enter. |
| Market inputs: no sliders | Prevents panel overflow on mobile and desktop side-by-side. OOB warnings preserved. |
| Financial snapshot display-only | PMI, income check, transaction costs — all derived, never fed back to engine. |
| `otherItemized` UI removed, engine param retained at 0 | Irrelevant to target audience. Full engine removal deferred to v4.0. |
| Budget-first closed-form formula reverted | Same total budget produced different derived home prices across locations — confused testers. Preserved in research docs for potential v4 reintroduction. |

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
- New React hooks must be added to destructuring on line ~87
- The assembled `index.html` is the single source of truth
- **Update CHANGELOG AND PROJECT_INSTRUCTIONS when a version is feature-complete, not when shipped.** Feature-complete = edits stopped and scope committed. Shipping is Allan's async step. Both files update together: CHANGELOG gets the new version entry, PROJECT_INSTRUCTIONS updates the "Current State" header, adds the version row to the versioning table, and prunes completed items from the Pending list (moving any carry-overs to the next version's section). Keeping the trigger at feature-complete prevents undocumented version stacking and keeps context-doc and history-doc in sync across session handoffs.

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
| v3.9 | Panel Polish | Guide closed default, home price callout, break-even clarity, theme CSS audit |
| v3.9.1 | Visual Polish | Sand removed, home price in card, theme groups, disclaimers themed |
| v3.9.2 | Theme Cleanup | Slate removed, Aqua/Gold added, override chips removed, header consolidated |
| v3.9.5 | Mobile CSS | Overflow fixes, touch targets, panel stacking, Gold/Midnight removed |
| v3.9.7 | CSS Overhaul | Inline tooltips, market sliders removed, clamp()/min() fluid CSS, chart fixes |
| v3.9.8 | Legal | Legal disclaimers doc, MIT LICENSE, tutorial fix, footer legal upgrade |
| v3.9.9 | Financial Snapshot | Down% callout, PMI/income/costs display, discipline→75%, Citrus, Ocean+Nord merge |
| v3.9.95 | ROI & CAGR | Both-sides ROI verdict, Investment Discipline rename, buyer cost breakdown, NaN fix, sim length=term+5 |
| v3.9.96 | Chart Consolidation | 4 charts (combined net worth, sensitivity redesign, combined equity+portfolio), callout centered, sell hint |
| v3.9.97 | Bug Fixes, CSS Touch-Ups | Sensitivity bar color/label fixed, PMI consistency across callouts |
| v3.9.98 | Your Situation Overhaul | Rent/P&I split with link, Savings/Down Payment label, Discipline 75%→65%, Other Itemized removed from UI, personal-input amber warnings |
| v3.9.99 | What-If Overhaul | Scenario→What-If (hidden default), Custom→Your Assumptions, two-panel default, color logic flipped, engine startRent pass-through |
| v3.9.99.1 | Micro-polish | intro-glow animation, og:image meta, amber warning wording |
| v3.9.99.2 | UI/UX Batch | "Rent vs Buy" header, Guide CTA, verdict winner headline, preset tracking, greyed metro dropdown |
| v3.9.99.5 | Style/Structure/Language | Banner→Guide summary, Share→panel header, verdict split into two widgets, confidence gating, hints auto-dismiss, VerdictBox KPIs |
| v3.9.99.6 | UI/UX Polish | Location merged into Your Situation, chart dropdown as title, Sim Length moved, tutorial toggle below themes |
| v3.9.99.7 | SEO/Webdesign | theme-color, favicon, apple-touch-icon, LICENSE link fix, savings hint breakdown |
| v3.9.99.8 | Math & Rigor | Input boundary hardening (`clampPersonal`), Felix 5% rule inline cross-check + Methodology note, "What is Felix?" scroll-and-highlight pattern, renters insurance removed from default engine calc |
| v3.9.99.9 | UX & Pre-v4 Polish | FX inline in Location, iOS/Android inputMode + autocomplete, disclosure ▶ arrow, labelBox rect+border chart labels, themes 8→6 (Citrus/Moss removed, light reordered), chart dropdown reorder (Sensitivity 1st), Paid off marker on all 3 line charts, print stylesheet |
| v3.9.99.10 | Small UI Polish | Pill colors use accent, Location/Your Assumptions/What-If titles centered on own lines, Guide summary renamed, sync-discipline rule codified |

---

## Pending Items

### v3.9.99.11 — Glossary Tab (content build)
- [ ] **New "Glossary" tab in Guide widget** — positioned right of FAQ, 5th tab. Umbrella for all definitions (starting with table columns, open to expansion for input terms, market concepts, etc.).
- [ ] **15 data-table column definitions in formula + worked-example format** — Each entry gets an anchor ID for jump-linking. Format: "Column name · Formula · Example: $500K − $300K = $200K · One-line note on interpretation." Upgrades `TABLE_TIPS` content quality simultaneously (current one-sentence definitions get richer).
- [ ] **No UI wiring yet** — iteration 3 handles link migration. Existing `<Tip>` tooltips continue to work during the interim so the table remains usable.

### v3.9.99.12 — Glossary Link Migration
- [ ] **`<GlossaryLink term=.../>` component** — Reusable, follows the Felix `methodHighlight` pattern from v3.9.99.8. Click opens Guide `<details>`, switches to Glossary tab, scrolls to anchor, flashes definition for 2.5 seconds.
- [ ] **Replace 15 `<Tip>` tooltips in data-table header** — Primary target. Fixes Allan's brother's "table numbers don't trace easily" feedback and the narrow-cell tooltip overlap.
- [ ] **Shorten inline tooltip content where callouts migrate** — e.g., "Mortgage Interest Rate. Most Impactful Variable. 1%..." becomes just "Mortgage Interest Rate" inline; the rich "most impactful" note + examples live in the Glossary. Hover still works for quick recall; click jumps for full context.
- [ ] **Leave input-field `<Tip>` tooltips alone for now** — hover-in-context works fine for short input definitions. Revisit in a future polish pass if needed.

### Deferred / Low-priority observations
- [ ] **Android inputMode verification** — iPhone 13 mini confirmed in v3.9.99.9; Android keyboards unverified. Will surface via user feedback if broken.
- [ ] **`autoComplete="off"` autofill behavior** — suppressed in v3.9.99.9 code; confirm no false-positive autofills on actual browsers.
- [ ] **Narrow-desktop MarketInputs layout wrap** — at roughly ~420-680px viewport widths (unusual desktop-with-narrow-window use case), `MarketInputs` labels can wrap and push the `?` tooltip trigger to a new line, producing visual oddities like "Appreciation\n?" or "Rent Growth\n?". Observed once in the wild during a session resize. Not a common use case; indirect improvement from v3.9.99.12 (shorter tooltip triggers → less horizontal pressure), full fix would require stacking labels above inputs at narrow widths or using a min-width media query. Deferred unless it surfaces in real use.
- [ ] **"Show values / Show YoY change" toggle** above table — prominent, not subtle; flips every cell between absolute and year-over-year delta
- [ ] **Inflation ↔ rent-growth default-tie** with unlink affordance

### v4.0 — Input Architecture Rework + Mode Toggle
- [ ] **Budget-first architecture (beginner mode):** Three primary inputs — **Rent Budget** (standalone, no link), **Monthly Buy Budget** (all-in: P&I + tax + ins + maint + PMI), **Savings Budget** (all-in: down payment + closing costs). Engine derives home price via closed-form from (S, M, t, k, A): `homePrice = (M + S·A − PMI) / ((1+k)·A + t/12)`. Addresses "closing costs ambushed me" user feedback — current UI makes buyers feel costs are hidden.
- [ ] **Mo.Investments as optional third-lane input** — Rendered next to Discipline slider as derived readout by default (`(Buy − Rent) × Discipline`). Unlock icon exposes direct $ input. When unlocked: Discipline auto-snaps to 100% but remains user-adjustable (covers "I invest $500 but miss a month a year = 92%"). Re-linking restores prior Discipline value. Inflation-adjusted by default (matches real household contribution behavior).
- [ ] **Standard vs Expert mode toggle** — Beginner mode: ~6 inputs (rent, buy, savings, discipline, mortgage term, sell year). Expert mode unlocks: component overrides (P&I, tax, ins, maint individually); Rent/Buy link toggle returns for apples-to-apples; SALT cap, AMT, NIIT, `otherItemized`, rental-income-if-moved-out; Mo.Investments inflation adjustment toggle (nominal vs real); closing-cost capitalization to basis (audit item #8); **extra monthly mortgage payment input** (goes straight to principal, shortens effective term, adjusts payoff marker — poor man's refinance precursor to the v5+ refi feature). Along with extra-payments, surface a **teachable prepay-vs-invest callout** (Felix-style heuristic, not prescriptive): "Adding $X/mo extra pays off the mortgage Y years early, saves $Z in total interest. That same $X/mo invested at [engine's invest return] would grow to $W. Prepaying is a certain [mortgage rate]% return; investing is an expected [invest return]% with volatility." Transparent, non-prescriptive, mirrors Felix's design philosophy. Warning on toggle: "Adding detail doesn't always improve accuracy."
- [ ] **Expert-mode clamp relaxation** — Hard clamps (from v3.9.99.8) stay on. In Expert mode, widen the *soft* amber-OOB ranges so expert users see fewer warnings for unusual-but-valid values (e.g., 50-year hold, 90% discipline).
- [ ] **Full PMI engine integration** — currently display-only estimate.
- [ ] **`otherItemized` full engine removal or Expert-mode surface** — UI already removed in v3.9.98; engine param still at 0. Either delete fully or wire into Expert mode.
- [ ] **`renterIns` — decide fate** — zeroed in v3.9.99.8 with architecture preserved. Either delete fully or resurface as an Expert-mode optional add.
- [ ] **Validation risk:** "Same budget → different home price per location" was the failure mode that killed the v3.9.99 budget-first attempt. New framing (total budget, not P&I) should defuse this because total housing cost naturally varies by location, but one fresh-tester walk-through is required before commit.

### v4.1+ — Enhanced Interactivity
- [ ] FAQ accordion
- [ ] Tutorial glow/highlighting
- [ ] Cross-link to brother's HPC tool

### Future (v5+)
- [ ] **Cash flow / scenario-analysis tab** — Granular cash-flow breakdown beyond rent/buy/invest. Scenario modeling for life events: car purchases, long-term care, inheritance, job change, kids. Out of scope for the core rent-vs-buy decision but natural extension of the financial-planning shape.
- [ ] Refinance toggle
- [ ] HELOC toggle
- [ ] RNG / Monte Carlo simulation
- [ ] Full PMI with credit score tiers

---

## Known Issues / Feedback

1. **Home price identical across locations within country** — correct behavior (same P&I + same rate = same derived home price), but counterintuitive for first-time users. VerdictBox KPIs explain what differs per location (appreciation, tax, spread).
2. **Old shared URLs** — `monthlyPayment` query param maps to both `rentBudget` and `buyBudget` for backward compatibility.
3. **Brother feedback:** year-by-year data table numbers don't trace easily back to formulas — addressed in v3.9.99.9 plan via `TABLE_TIPS` upgrade and YoY toggle.
4. **`mortgageTerm = 0` divides by zero** — audit-flagged; addressed in v3.9.99.8 input hardening.

---

## Design Philosophy

1. **Quality over features.** A correct model with 10 inputs beats a buggy model with 30.
2. **Accuracy over advocacy.** The model reveals which path wins under which assumptions. It does not lean.
3. **Explainability over aesthetics.** Every result traces to inputs.
4. **Simplicity over completeness.** If a feature can't be implemented correctly, document it as a text note.
5. **Customizability as credibility.** Users control all assumptions and see how results change.
6. **Open source as trust.** Every formula, default, and data source is documented and accessible.

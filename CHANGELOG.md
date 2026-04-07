# Changelog

All notable changes to the Rent vs. Buy Financial Simulation Engine.

---

## v3.5 — April 2026

### UI/UX Overhaul
- **Theme system:** 7 color themes — Light (default), Dark, Midnight (terminal-style), Warm (reduced eye strain), Slate (neutral professional), Ocean (dark blue marine), Ember (dark warm). Theme selector rendered as colored pill buttons in the header. Theme persists via shareable URLs.
- **Chart-table merge:** Data table is now an expandable section ("▸ View Year-by-Year Data") inside the chart card, replacing the separate bottom card. Cleaner layout, one less scroll target.
- **Chart clarity:** Chart dropdown labels rewritten for non-technical users ("Who wins & by how much", "What You Pay Each Year", "What matters most"). Subtitles explain each view in plain English.
- **Micro-interactions:** Slider filled-track gradient shows position, input border highlight on hover, smooth button transitions, slider thumb glow on hover. All backed by UI/UX research (Stripe, Vercel, Bloomberg patterns).
- **Mobile responsive:** Panels auto-stack below 320px via CSS auto-fit grid. Input font bumped to 16px to prevent iOS Safari zoom. Touch targets minimum 44px.
- **Disclaimers:** "Educational only — not financial advice" placed in 4 locations (header, guide, bottom line callout, footer) without being overbearing.
- **Footer:** Links to Allan Comeau's LinkedIn and GitHub source repository.
- **Page title:** Updated for LinkedIn link previews ("Should I Rent or Buy? | Interactive Financial Simulation").

### Research & Documentation
- Added 6th research file: UI/UX Design Research (design tokens, typography, layout patterns).
- All 6 research docs hyperlinked in Sources & Limitations tab.

### Code Quality
- Removed dead `tableSync` state and `TABLE_COLS` object (dead code from table merge).
- Theme system uses module-level `let T` updated on each render — minimal refactoring, zero prop-drilling.
- CSS custom properties (`--slider-bg`, `--slider-thumb`, `--tut-*`) enable theme-aware pseudo-element styling.

---

## v3.0 — April 2026

### Architecture
- **Monthly-Payment-First:** Primary inputs are Monthly Payment ($) and Down Payment ($). Home price derived via backsolve formula.
- **Three-panel layout:** Location (locked reference, dual-metro with 🔗 link toggle), Scenario (12 presets, editable), Custom (fully editable sandbox).
- **Single HTML file:** No build tools, no backend. React 18 + Recharts via CDN (unpkg). Babel Standalone for JSX compilation.
- **Engine as pure function:** `simulate()` is deterministic — same inputs, same outputs. Zero side effects.

### Features
- 16 countries, 54 metros with sourced data (FHFA, ABS, ONS, Destatis, etc.)
- 3 international tax buckets: Bucket A (no deduction), Bucket B (NL/SE partial), US (full excess itemization)
- 12 presets: Match reference, Low/High rate era, Conservative, Hot market, Stagnant, Inflation surge, Rate cuts, Stock underperformance, Housing correction, Best case buy/rent
- 6 chart types: Advantage (all scenarios), Wealth paths, Annual cost, Equity buildup, Portfolio comparison, Sensitivity tornado
- Sensitivity analysis: ±delta on 6 variables, sorted by impact, tornado visualization
- Combined Bottom Line callout with 7 conditional insights (🟢 Robust, 🟡 Fragile, 🔴 Highly assumption-dependent, ⏱ breakeven proximity, 💡 discipline, 📌 negative spread, ⚠ high appreciation)
- Click tooltips (?) on all inputs and table column headers (TIPS + TABLE_TIPS)
- Sliders on all market inputs + personal inputs with out-of-bounds amber indicator
- Editable FX rates with USD conversion display for non-US countries
- URL state sharing (base64 hash encoding)
- Tutorial mode (6 guided steps)
- Guide open by default with 3 tabs (How It Works, Methodology, Sources & Limitations)

### Engine Bug Fixes (from math audit)
- Bug 1: Final mortgage payment now uses `mi + mp` instead of full `monthlyPmt` (was overstating buyer costs in payoff year)
- Bug 2: Selling costs now reduce capital gain per IRS Publication 523 (`homeVal - sellCosts - homePrice`)

### Research Documents
- `rent_vs_buy_research_report.md` — Academic foundations (Poterba, Shiller, Jordà, Himmelberg, Ben Felix)
- `rent_vs_buy_research_report_v2.md` — Volatility drag, arithmetic vs geometric means
- `defaults_rationale.md` — Every default value sourced, 16 countries, 475 lines
- `what_model_doesnt_capture.md` — Known limitations with directional bias analysis
- `rent-vs-buy-sim-audit.md` — Formula-by-formula math verification, 23 items reviewed

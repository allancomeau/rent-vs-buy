# Changelog

All notable changes to the Rent vs. Buy Financial Simulation Engine.

---

## v3.9 — April 2026

### Panel Polish & Discoverability
- **Guide closed by default:** Removed `open` attribute from guide section. Tutorial nudge and toggle handle discovery. Addresses #1 feedback item across all testers — wall of text before inputs killed engagement.
- **Home price derivation callout:** Prominent centered line between "Your Situation" and panels: "At 6.0% rate, $2,000/mo + $80,000 down = $413,583 home." Makes payment-first architecture immediately visible.
- **Break-even clarity:** "BE Never" replaced with "Break-even: Never" across all 3 panel locations. Hover tooltip explains "First year where buying catches up to renting in total wealth."
- **Full theme CSS audit:** Eliminated all remaining hardcoded rgba colors from UI components. VerdictBox, Location panel header, Scenario panel, Link toggle, tornado chart bars, and overrides display all now use `T.*` theme tokens with hex alpha. Every element adapts to every theme.

---

## v3.8 — April 2026

### Layout & Communication
- **Layout reorder:** "Your Situation" inputs and three panels (Location, Scenario, Custom) now grouped together as inputs. Verdict callout moved between panels and chart — flows as: configure → see result → visualize.
- **Verdict explanations:** Each confidence indicator (🟢🟡🔴) now includes a plain-English follow-up explaining what it means for the sensitivity chart and the user's decision.
- **FAQ scroll-to:** Clicking the FAQ link in the tutorial nudge now scrolls the page to the guide section instead of just expanding it silently.
- **Custom panel dynamic labeling:** Custom panel title shows "Custom (edited)" when overrides don't match any preset, helping users distinguish between preset-loaded and manually-adjusted scenarios.
- **Disclaimer high-contrast:** "Educational only — not financial advice" rendered in red (#dc2626) wherever it appears for emphasis.
- **Banner update:** Open source banner now mentions "Built with Claude Opus 4.6."
- **Theme CSS audit:** Focus/hover states, input borders, slider thumb glow, and text selection now all use CSS custom properties that update with each theme. Eliminated 8 hardcoded color values from the CSS `<style>` block.
- **Tornado chart:** Moved to 2nd position in chart dropdown (after Total Wealth). Star (★) now has hover tooltip explaining "Most impactful variable."
- **Verdict colors:** verdictLabel function already theme-aware via T parameter — verified and confirmed working across all 10 themes.

---


### UX & Communication
- **Open source banner:** Dismissible banner at top emphasizing open source, open research, and GitHub access. Theme-aware colors. Closes for session, reappears on refresh.
- **Tutorial toggle switch:** Replaced button with iOS-style toggle for Tutorial on/off. Hover tooltip explains function.
- **Chart auto-description:** Each chart shows a collapsible explanation panel that auto-opens when switching chart types. Click to collapse, click "What does this chart show?" to reopen.
- **Large-advantage explainer:** When advantage exceeds $200K at 100% discipline, an inline explanation appears: why the number is so large, what drives it, and suggesting 70% discipline as a reality check.
- **FAQ tab expanded:** Added link to full 115-question FAQ research document on GitHub, with Ctrl+F search hint. FAQ.md added as 7th research file.
- **Emoji cleanup:** Removed decorative emojis (🎓📋📖📄) from tutorial, share, guide, and research links. Kept functional emojis (🟢🟡🔴💡📌⚠️⏱🔗🔒) that serve identification purposes.
- **Share link tooltip:** Hover text explains "Saves all your inputs as a shareable URL" since the subtitle was removed in header centering.
- **Header polish:** Tutorial, theme, and share controls in a clean centered row. Tutorial label highlights in accent color when active.

### Documentation
- 7th research file: FAQ.md — 115 research-backed questions across 11 sections (common criticisms, investment returns, tax treatment, behavioral finance, housing market, renting variables, transaction costs, what models miss, international, analytical approaches, practical decision-making).

---

## v3.7.5 — April 2026

### Theme Bug Fixes
- **Midnight pill fix:** Active pill now uses `v.card` background + `v.text1` text — guaranteed contrast for every theme.
- **3 new themes:** Nord (Scandinavian blue-gray), Sand (warm paper/desert), Moss (forest green dark). 10 themes total.
- **Default chart:** Changed from Advantage to Total Wealth — non-alarming first impression.

---

## v3.7 — April 2026

### UX & Communication
- **Open source banner:** Dismissible banner emphasizing open source + Git links. Theme-aware colors. Closes for session, reappears on refresh.
- **Tutorial toggle switch:** Replaced button with iOS-style toggle. Hover tooltip explains function.
- **Chart auto-description:** Collapsible explanation panel that auto-opens when switching chart types.
- **Large-advantage explainer:** When advantage exceeds $200K at 100% discipline, inline explanation with compound math and 70% discipline suggestion.
- **FAQ tab expanded:** Link to full 115-question FAQ on GitHub with Ctrl+F hint. FAQ.md added as 7th research file.
- **Emoji cleanup:** Removed decorative emojis (🎓📋📖📄). Kept functional (🟢🟡🔴💡📌⚠️⏱🔗🔒).
- **Share link tooltip:** Hover text explains "Saves all your inputs as a shareable URL."

---

## v3.6 — April 2026

### UX
- **Header centered:** Question-as-headline ("Should I Rent or Buy?"), centered layout with theme/tutorial/share controls in a row below.
- **FAQ tab:** Added 4th guide tab with 7 inline questions addressing common objections (returns comparison, tax exclusion, renting wins, own math validation, quality gap, trust, practical use).
- **Tutorial nudge:** One-line hint below header when tutorial is off, linking to Tutorial and FAQ.

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

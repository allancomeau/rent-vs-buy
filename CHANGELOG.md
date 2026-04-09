# Changelog

All notable changes to the Rent vs. Buy Financial Simulation Engine.

---

## v3.9.9 — April 2026

### Financial Snapshot & Polish
- **Financial snapshot callout:** Home price widget rebuilt with display-only insights: editable down payment %, supported home price, PMI estimate (~$X/mo with rate by bracket), income reality check (28% front-end ratio), round-trip transaction costs, and national median comparison ("near median" / "well below" / "above"). Engine untouched — all display-layer math.
- **Investment Discipline renamed:** "Renter Discipline" → "Investment Discipline" across all files. TIPS tooltip updated to clarify it applies to both sides' surplus cash, not just renter. Reflects Bernstein-Koudijs finding that forced mortgage savings is the asymmetry, not the discipline rate itself.
- **Discipline default 75%:** Changed from 100% → 85% → 75% (top of research-backed realistic range). Slider max set to 75 — typing higher is allowed but shows amber out-of-bounds. No amber warning on load.
- **Citrus theme added:** Warm yellow-orange light theme. Accent #E08A10, bg #FFFDF0. Vibrant but readable.
- **Ocean absorbs Nord:** Ocean slightly more vibrant (#5CB8FF accent, #50E0A0 green). Nord removed — too similar.
- **8 final themes:** Light → Dark ordering. Light: Light, Warm, Aqua, Citrus. Dark: Dark, Ocean, Ember, Moss. Single separator between groups.
- **Disclaimer high-contrast audit:** Header, guide, verdict, and footer disclaimers all use theme-aware accent backgrounds with bold text. Footer upgraded with employer separation and legal link.

---

## v3.9.8 — April 2026

### Legal & Accessibility
- **Legal disclaimers document:** `legal_disclaimers.md` added — six-component disclaimer (purpose, no relationship, hypothetical, professional referral, user responsibility, employer separation) + privacy statement + ALL CAPS warranty disclaimer per UCC §2-316(3).
- **MIT LICENSE file:** Standard MIT license added to repo root. GitHub auto-detects and shows badge.
- **Tutorial text overlap fixed:** Steps 3 and 5 had `<strong>` tags inside flex container causing column layout on mobile. Text wrapped in `<span>` so flex only sees badge + text block. `align-items: flex-start` for long text.
- **Footer legal upgrade:** "Independent personal project" + employer separation + "Full Legal Disclaimers & Privacy" link. Three-line footer with all six legal components represented.
- **Legal Disclaimers added to RESEARCH_FILES:** 8th research document, visible in Sources & Limitations guide tab.

---

## v3.9.7 — April 2026

### CSS Overhaul & Mobile
- **Inline tooltips:** Replaced `position: absolute` popup tooltips with inline display below input. Click `?` → tip appears in document flow. Click again or tap tip → closes. Zero positioning, zero viewport bleed, works on all screen sizes. Removed `tipBox` style object entirely.
- **Market input sliders removed:** Scenario/Custom panel inputs now clean number fields with `1fr 1fr` grid. Out-of-bounds amber warnings preserved. Personal inputs keep sliders. Fixes both mobile and desktop panel overflow.
- **CSS modernization from research:** `clamp()` for fluid h1 (24px–32px) and page padding (12px–32px). `min()` inside `minmax()` for panel and input grids — mathematically prevents grid overflow. Container width via `min(100%, 1400px)`. Removed redundant media query overrides.
- **Mobile chart fixes:** Margins reduced (30/20 → 10/5), Y-axis width reduced (70 → 55px), axis text 9px at 480px breakpoint.
- **Theme pills on own row:** Tutorial + Share Link share one line, themes sit below. No layout shift when active theme name changes.
- **Page overflow prevention:** `box-sizing: border-box`, `overflowX: hidden` on page container.

---

## v3.9.5 — April 2026

### Mobile CSS Pass
- **Body overflow-x hidden:** Prevents horizontal scroll globally.
- **Page padding reduced:** 32px → 16px for mobile breathing room.
- **Panel grid 280px min:** Down from 320px — fits narrow phones.
- **Data table scrollable:** `overflowX: auto` with `-webkit-overflow-scrolling: touch`.
- **Touch targets 44px:** All buttons and inputs meet Apple HIG minimum.
- **Input font 16px:** Prevents iOS auto-zoom on focus.
- **480px breakpoint:** Chart axis text, legend, and body font size adjustments.
- **Gold + Midnight themes removed:** Gold redundant with Warm, Midnight sacrificed readability. 8 themes remaining.

---

## v3.9.2 — April 2026

### Theme & Header Cleanup
- **Slate removed, Aqua + Gold added:** Slate too similar to Light. Two new distinct themes.
- **Home price enriched:** Shows loan amount + down percentage.
- **Override chips removed:** "Invest 7.0% · Appr 3.0%" display eliminated — redundant with slider values.
- **Tooltip overflow fixed:** Removed `overflow: hidden` from panel, added `borderRadius` to panel header.
- **Header consolidated:** Subtitle, tutorial link, FAQ link, and disclaimer in clean two-line flow. Removed separate nudge block.

---

## v3.9.1 — April 2026

### Visual Polish
- **Sand theme removed:** Too similar to Warm. 9 themes.
- **Home price moved inside Your Situation card:** No longer a separate floating element.
- **Theme pills grouped:** Subtle 1px separators between Core, Extended, and Additional groups.
- **Tutorial toggle prominent:** Accent-colored background box, bolder text.
- **Share Link prominent:** fontWeight 700, larger padding.
- **Disclaimers theme-aware:** Replaced all `#dc2626` red with `T.accent` background highlights. Header disclaimer upgraded from invisible `T.text3` to visible `T.text2` with background.
- **Toggle switch knob:** Uses `T.card` instead of hardcoded `#fff`.

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
- `rent-vs-buy-sim-audit.md` — Formula-by-formula math verification, 23 items reviewed- **3 new themes:** Nord (Scandinavian blue-gray), Sand (warm paper/desert), Moss (forest green dark). 10 themes total.
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

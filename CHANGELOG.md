# Changelog

All notable changes to the Rent vs. Buy Financial Simulation Engine.

---

## v3.9.99.12 — April 2026

### Glossary Link Migration
- **`GlossaryLink` component** — Defined inside `RentVsBuyV3` (closure access to `setGuideTab`, `guideRef`, `setMethodHighlight`). ~13 lines. Renders children inside a `<span>` with `cursor: help`, `title={short}` for hover tooltip, and click handler that switches to the Glossary tab, force-opens the Guide `<details>`, scrolls the target anchor into view (`block: "center"`), and flashes via the existing `target-highlight` CSS animation for 3 seconds. Reuses all v3.9.99.8 Felix infrastructure — zero new state, zero new CSS.
- **Derivation for hover tooltip** — Short definition derived from the first sentence of each `GLOSSARY[n].def` via regex `/^[^.]*\./`. No separate `short` field, no duplicate copy. Single source of truth for column definitions: the GLOSSARY array.
- **15 data-table header `<Tip>` calls migrated** — Each `<Tip text={TABLE_TIPS[h]}>{h}</Tip>` became `<GlossaryLink term={h}>{h}</GlossaryLink>`. Visible change: no more `?` suffix on column headers, `cursor: help` on hover, browser `title` tooltip for quick recall on desktop, tap/click jumps to full Glossary entry on both desktop and mobile.
- **`TABLE_TIPS` deleted** — 25-entry const pruned to 15 in v3.9.99.11, then fully deleted here. Dead code removed; GLOSSARY is the sole source of truth for column definitions. Closes a two-version redundancy cleanup.
- **Interaction hint above data table** — Short italic muted note: "Hover a column header for a quick definition · click to jump to its full Glossary entry with formula and example." Renders only when the table is expanded, so it's contextual to where users see the new interaction and doesn't clutter the collapsed state.
- **`<Tip>` component preserved** — 1 remaining caller (NumInput input-field labels), fine as-is. Input-field `<Tip>` migration deferred — hover-in-context works well for short input definitions, per the v3.9.99.11 scope decision.

---

## v3.9.99.11 — April 2026

### Glossary Tab (content build)
- **New "Glossary" tab in Guide widget** — 5th tab, positioned right of FAQ. Reuses existing `S.tab` / `S.tabA` styling for visual consistency with the other four tabs. Tab ID = `"gl"`.
- **`GLOSSARY` const array (module-level)** — 15 entries for the data-table columns, driven by structured data rather than hand-written JSX. Each entry: `{id, name, def, formula?, ex?, note?}`. Optional fields render conditionally — entries without a formula just skip the formula line. Future additions are array entries, not JSX edits.
- **Rich entry format** — Each glossary entry includes a 1-2 sentence definition, optional formula (monospace), worked example, and interpretation note where applicable. Column name rendered in monospace (`THEME_MONO`) inside a `<strong>` so it visually matches how the header appears in the actual data table. Example: `Equity — Your ownership stake... | Home value − Mortgage balance | Example: $500K − $300K = $200K | Note: Selling costs (~6-8%) reduce actual take-home.`
- **Anchor IDs ready for iteration 3** — Each entry renders with `id="gl-<name>"` and `className={methodHighlight===g.id?"target-highlight":""}` — same pattern as the Felix methodology entry in v3.9.99.8. v3.9.99.12's `<GlossaryLink>` component can use the existing `methodHighlight` state and `target-highlight` CSS animation without new infrastructure.
- **`TABLE_TIPS` pruned 25 → 15 keys** — Removed 10 stale aliases left over from earlier column renames: `"Buyer W"`, `"Renter W"`, `"Advantage"`, `"Home Value"`, `"Yr Principal"`, `"Balance"`, `"Buyer Cost"`, `"Renter Cost"`, `"Buyer Inv"`, `"Renter Inv"`. Active inline `<Tip>` tooltips still work via the 15 remaining keys — each of the 15 table headers matches exactly one key. Aligns with the v3.9.99.10-codified redundancy principle (no duplicated state, definitions, constants, or code).
- **No UI wiring yet** — existing `<Tip>` tooltips in table headers continue to function; iteration 3 (v3.9.99.12) handles the click-to-jump migration. Table remains fully usable during the interim.
- **Scale pattern deferred, not scaffolded** — Considered implementing nested `<details>` subsections inside the Glossary tab preemptively (to segment future "Data table columns" / "Input callouts" / "Market concepts" sections). Shipped flat instead. Over-engineering for the current 15-entry state. The `.disclosure-icon` CSS class from v3.9.99.9 is already reusable if/when we cross ~25-30 entries and need structural subdivision.

---

## v3.9.99.10 — April 2026

### Small UI Polish (glossary build preparation)
- **Theme pill colors** — Inactive pill background switched from `v.bg` (pale theme background) to `v.accent` (theme's signature accent color). Each pill now reads as a clearly identifiable colored dot representing its theme: Light → blue, Aqua → teal, Warm → orange, Dark → bright blue, Ocean → light blue, Ember → bright orange. Fixes a persistent issue where the Warm theme's pill read as pink/cream because its `bg` (`#FBF7F2`) was too desaturated to convey "orange" in a 20px dot. Tradeoff: users can no longer tell light vs dark theme from pill background alone (all are colored dots now); mitigated by the light/dark divider still being visually obvious and the active pill still using `v.card` as its expanded background.
- **Section title centering — Location, Your Assumptions, What-If** — Three section headers restructured so the title lives on its own centered line with the control row (dropdowns, Share button, ✕ close) below. Previously all shared a single flex row with content, which cramped on desktop and broke on narrow mobile. Implemented via `ScenarioPanel` component unconditionally stacking its header (extracted `titleSpan`, `presetSelect`, `closeBtn` as variables to avoid duplication). Both Your Assumptions and What-If now render consistently.
- **Guide summary rename** — "Guide, FAQ & Methodology" → "Guide". The enumerated tab list doesn't scale — we're adding a Glossary tab in v3.9.99.11, and future tabs would make the summary unwieldy. "Guide" is umbrella-level and future-proof.
- **PROJECT_INSTRUCTIONS sync discipline codified** — Build Rules now explicitly require updating both CHANGELOG **and** PROJECT_INSTRUCTIONS when a version is feature-complete (current-state header, versioning table, pending-item pruning). Previous rule only covered CHANGELOG; PROJECT_INSTRUCTIONS had drifted — "Current State" still read v3.9.99.7 when we were on v3.9.99.9, and Pending Items held completed entries from v3.9.99.8 and v3.9.99.9. Now both files update together at feature-complete.

---

## v3.9.99.9 — April 2026

### UX & Pre-v4 Polish
- **FX converter merged inline into Location header:** Previously rendered as a separate card between Confidence and Chart; now appears in the LOCATION row after the country dropdown (conditional on `cur !== "USD"`) with a `·` separator. Row uses `flexWrap: "wrap"` so narrow mobile gracefully wraps the rate block to a second line instead of overflowing horizontally. "Reset" text button → `↺` icon with `title="Reset to default"`. "Default: X.XX (Apr 2026)" footnote moved from visible text into the input's `title` attribute (hover reveals). Standalone FX card deleted.
- **Mobile keyboard optimization:** Added `inputMode="decimal"` + `autoComplete="off"` to NumInput (all budget/rate/price fields) and FX rate input. `inputMode="numeric"` on simYears (integer-only, cleaner iOS number pad without decimal key). Confirmed working on iPhone 13 mini; Android unverified (DevTools doesn't emulate mobile keyboards).
- **Disclosure affordance on Guide `<details>`:** Added `▶` icon prefix (13px, `T.text2`) at the start of the Guide summary header. `.disclosure-icon { transition: transform 200ms ease }` + `details[open] .disclosure-icon { transform: rotate(90deg) }` — native CSS rotation on toggle. Full-header click target preserved. Reusable `.disclosure-icon` class ready for any future `<details>` sections.
- **Mortgage-payoff marker on Net Worth chart:** Previously only on Annual Cost and Equity charts. Now consistent across all three line charts (conditional on `mortgageTerm ≤ simYears`).
- **Reference-line label rewrite — `labelBox` helper:** Module-level render function produces rect + colored border + text for every ReferenceLine label. Reads `T.card` at call time → theme-aware fill behind text → dashed line no longer cuts through label text. Applied to all 7 label sites across 3 charts, replacing previous `position: "insideTop"` + stroke-halo approach. Added missing "Sell yr" label to Equity chart (previously had a reference line with no label).
- **Themes trimmed 8 → 6:** Removed Citrus (light) and Moss (dark). Light group reordered Light → Aqua → Warm to parallel dark's cool-before-warm order (Dark → Ocean → Ember). Line 867 `T = THEMES[theme] || THEMES.light` fallback handles old shared URLs with `?theme=citrus` or `?theme=moss` — graceful degradation to Light.
- **Chart dropdown reorder:** Sensitivity promoted to position 1, Net Worth to position 2. Default `chartType` state unchanged (`"networth"`) so the page still loads to the line chart — non-alarming first impression preserved. Scrolling through the dropdown now flows bars → line → line → line (single bar-to-line transition instead of two).
- **Print stylesheet:** `@media print` block with 0.5in page margin, forces white bg + black text on all non-SVG elements (`*:not(svg):not(svg *)`) so chart colors survive, hides interactive noise (buttons, sliders, tutorial, disclosure icons, Recharts tooltip wrapper), renders `<select>` values as plain inline text via `appearance: none`, `page-break-inside: avoid` on chart SVG and legend. Tested and confirmed working on real print-preview.
- **Itemization heuristic — added then removed:** Briefly added an "Itemize check" bullet to the financial-snapshot details block (year-1 interest + SALT-capped property tax vs standard deduction, with benefit estimate). Removed on user feedback: engine already handles itemization correctly via excess-itemization method (the data table's `TaxB` column), and the heuristic produced "take standard deduction" for ~70% of target users (boring result). Scope discipline — heuristic callouts should earn their pixels, and we removed `otherItemized` input in v3.9.98 for the same reason.
- **Post-deploy validation checklist documented:** `inputMode` iOS keyboard behavior, `autoComplete="off"` autofill suppression, "Paid off" label visibility on high-advantage scenarios — tracked in PROJECT_INSTRUCTIONS as pre-v4 gate.

---

## v3.9.99.8 — April 2026

### Math & Rigor
- **Input boundary hardening:** `PERSONAL_CLAMPS` constant + `clampPersonal(p)` helper added at top of engine section. Wraps the `useState` initializer (catches URL-restored bad values), the `up` state-setter helper, and all 4 inline `setPersonal` call sites (country change, linked Rent Payment, linked Mortgage P&I, simYears input). Silently coerces out-of-range values to prevent engine NaN/Infinity. Hard clamps: `rentBudget [0, 100K]`, `buyBudget [0, 100K]`, `downPayment [0, 10M]`, `mortgageTerm [1, 50]`, `holdingPeriod [1, 100]`, `simYears [5, 100]`, `discipline [0, 1]`, `otherItemized [0, 1M]`. Dependent-bound rule: `holdingPeriod ≤ simYears` enforced after individual clamps. Engine untouched — sanitization lives at the state boundary. 13/13 adversarial unit tests passed (audit bug `mortgageTerm = 0`, negatives, out-of-range, NaN tolerance, valid-input preservation). Resolves audit item #7 ("mortgageTerm = 0 divides by zero").
- **Ben Felix 5% rule cross-check (dynamic):** Added inline block to the Confidence & Warnings card showing `homePrice × 5% / 12` as break-even rent, compared to user's entered rent. Three verdict zones: ≥10% below break-even → green "renting favored," within ±10% → text2 "close to threshold," ≥10% above → accent "buying favored." Zero-guard early-returns on degenerate homePrice or rent values. Provides an external calibration signal without displacing the simulation's own verdict — visible disagreement is a feature, prompts user to consider which model's assumptions they trust.
- **"What is Felix?" clickable jump-to-methodology:** Link in the Felix block opens the guide `<details>` element, switches to the Methodology tab, scrolls into view, then flashes the Felix paragraph for 2.5 seconds via a new `target-highlight` CSS animation. Uses existing `--slider-thumb` CSS custom property → theme-aware across all 8 themes. Pattern is reusable (`methodHighlight` state with string key, applied as conditional className) for future jump-and-highlight affordances from other callouts.
- **Methodology note on heuristic cross-check:** 86-word paragraph added to Methodology tab explaining Felix's 5%-rule derivation (property tax + maintenance + cost of capital − appreciation), US-calibration caveat, and its role as a second opinion rather than a replacement for the simulation.
- **Renters insurance zeroed across all 16 countries:** Only ~14% of renters voluntarily carry renters insurance; the rest have it because a landlord requires it (typically baked into their rental budget already) or don't carry it at all. Adding a flat $200–15,000/yr premium on top of user's declared rent double-counted for most users. Engine code untouched (multiplication by 0 is a no-op); country architecture preserved for potential v4 Expert-mode re-enable. `RntCst` table tooltip updated: "Total renter cost: annual rent (renter's insurance assumed budgeted in rent input)."
- **Script-tag count drift noted:** Pre-v3.9.99.8 PROJECT_INSTRUCTIONS verification check said `scripts: 8/8`; actual count is 9/9 since the JSON-LD WebApplication schema script was added in v3.9.99.7. No action — just a stale note.

---

## v3.9.99.7 — April 2026

### SEO / Webdesign
- **Theme-color meta + favicon:** `<meta name="theme-color" content="#2563eb"/>` colors the mobile browser URL bar. `favicon.png` + `apple-touch-icon` link tags added. PNG pushed to repo root.
- **LICENSE link fixed:** `LICENSE.txt` → `LICENSE` across 3 references (matches GitHub's actual filename).
- **Savings hint breakdown:** "19% down + $12K closing = $91K upfront · renter invests same" — explicit closing-cost component addresses feedback that the renter's starting investment felt like it appeared from nowhere.
- **OG tags + JSON-LD confirmed:** WebApplication schema, canonical URL, Twitter card, OG image all in place.

---

## v3.9.99.6 — April 2026

### UI/UX Polish
- **Location merged into Your Situation card:** single unified widget, no separate panel. Country dropdown sits in a centered "LOCATION · [Country]" header row with `·` separator. Metro selects + verdict boxes below a 2px border-top separator from the financial snapshot above.
- **Tutorial toggle on own line below themes:** no longer inside Guide summary. Themes solo centered row, tutorial toggle solo row below.
- **Guide summary condensed:** two centered lines — "Guide, FAQ & Methodology" / "Open source · GitHub".
- **Chart dropdown is the title:** 16px, centered, accent-bordered (`2px solid T.accent+44` with `T.accent+08` background). Dropdown itself serves as the chart title.
- **Sim Length moved to chart widget:** no longer a personal input. Lives alongside the chart where users look for it when interpreting long-horizon results.
- **Link buttons themed:** 1px accent border when linked, matches the Location link button style. Consistent clickable affordance.

---

## v3.9.99.5 — April 2026

### Style, Structure, Language Polish
- **Banner → Guide summary:** dismissible open-source banner removed. "Open source · nothing hidden · GitHub" absorbed into Guide summary. One less element above the fold.
- **Share button → Your Assumptions panel header:** via `extraHeader` prop on `ScenarioPanel`. Themes solo centered in page header.
- **Savings hint ordering fixed:** "Buyer puts X% down · renter invests $Y" now matches the "Savings / Down Payment" label ordering.
- **All disclaimers link to Legal + MIT License:** header, verdict, and Sources tab disclaimers all link to `legal_disclaimers.md` + `LICENSE`.
- **Verdict split into two widgets:**
  - **Verdict card** — winner headline centered at top, winner/loser details, linked disclaimer.
  - **Confidence & Warnings card** — separate widget with theme-aware colors (green when robust, amber when fragile or 1 warning, red when highly dependent or 2+ warnings). Tax notes, input warnings, large-advantage explainer all live here.
- **Confidence gating:** strong verdict (≥15% of home value) caps at 🟢. Prevents the contradiction where "Strong Rent +$68K" paired with "🔴 Highly assumption-dependent."
- **FX converter in own minimal card:** only renders for non-USD countries.
- **VerdictBox clickable KPIs:** click expands to show Break-even · Appr · Tax · Spread (invest − mortgage). No auto-dismiss.
- **Financial snapshot collapsible:** "Show details / Hide details" toggle. Home price + country median always visible; PMI threshold, income check, transaction costs hide by default.
- **Hints auto-dismiss at 8s:** `setTimeout` on `Tip` `show` state. Verdict KPIs exempt.
- **Consolidates .3 and .4 bulk bumps:** micro-iterations between .2 and .5 folded here for changelog clarity.

---

## v3.9.99.2 — April 2026

### UI/UX Batch
- **Header title:** "Should I Rent or Buy?" → **"Rent vs Buy"**. OG title updated to match. Shorter, more legible on mobile.
- **Guide CTA retitled:** "Tap here for the Tutorial, FAQ, or Guide" with "Methodology & Sources" subtitle. Tutorial toggle moved into the Guide summary bar with `stopPropagation` so clicking it doesn't open/close the Guide.
- **Verdict restructure:** winner headline centered at top — "Buying wins by 30.1% ROI (0.5%/yr)". Winner's details listed first, loser second. Color-coded by winner (accent for buyer, green for renter).
- **Sensitivity legend simplified:** removed "All variables adjusted by the same scale" text. Legend: "◀ −% buying renting +% ▶".
- **Your Assumptions preset tracking:** shows "Your Assumptions (Low Rate Era)" in purple when a preset is loaded. Clears immediately when any input changes. No continuous re-comparison against preset defaults.
- **Location metro dropdown greyed when linked:** replaces the "🔗 National avg. Click 🔗 for two metros." text. Link button inline next to metro dropdown. Visually aligned with budget UI.
- **Sub-callouts restored under budget inputs:** "Extra invested" under Rent Payment, "Buyer total" under Mortgage P&I.

---

## v3.9.99.1 — April 2026

### Micro-polish
- **`intro-glow` CSS animation:** 3s box-shadow pulse around tutorial/theme controls on load to aid discoverability.
- **`og:image` meta:** pointing at `preview.png` for LinkedIn/Twitter card rendering.
- **Rent vs P&I amber warning wording:** clarified to "Rent budget and mortgage P&I differ by more than 10% — comparison may not reflect equal affordability."

---

## v3.9.99 — April 2026

### What-If Overhaul
- **Scenario panel → What-If:** renamed and hidden by default. Dashed-border placeholder with "+ Add What-If" expands into the full panel. Two-panel default layout (Location + Your Assumptions) for first-time users.
- **Custom panel → Your Assumptions:** rename reflects role — not a "custom override" of a reference, it IS the user's scenario.
- **What-If narrow right column when expanded:** `flex: 1 1 200px`, capped at 200px when collapsed. Wraps below on mobile via `flexWrap: wrap`.
- **Color logic flipped:** Location is now the neutral reference (muted gray), Your Assumptions is the primary interactive panel (accent blue, or purple when a preset is active), What-If keeps purple.
- **Engine `startRent` pass-through:** `buildParams` now forwards `startRent` from `personal` rather than computing it inside `simulate`. Enables rent independent from P&I.
- **Three-panel → two-panel default:** addresses UX report's progressive-disclosure principle. Cognitive load for first-time users reduced.

---

## v3.9.98 — April 2026

### Your Situation Overhaul
- **`Monthly Payment` split into `Rent Payment` + `Mortgage P&I`:** two inputs with 🔗 link toggle. Linked by default (apples-to-apples comparison). Unlinked triggers >10% amber warning if values diverge significantly.
- **Savings / Down Payment label:** clarifies buyer uses this for down payment, renter invests the same total (down + closing). Hint renders "X% down · renter invests $Y".
- **Investment Discipline default: 75% → 65%:** middle of Bernstein & Koudijs (QJE 2024) 50–80% realistic range. More honest starting point — users see realistic numbers on load rather than near-perfect. Slider 30–80%, tip standardized to "50–80% realistic".
- **`Other Itemized` removed from UI:** irrelevant to target audience (first-time buyers on standard deduction). Engine param stays at 0 (no functional change). Full engine removal deferred to v4.0.
- **Amber warnings on personal inputs:** mortgage term outside 15–30yr, sell year <3 or >30, discipline >80% or <40% all trigger amber border + "Outside typical range".
- **Scope bug fix:** buyer-blurb IIFE was referencing `hp2`, `ln`, `pmt` from a sibling IIFE — would have white-screened on Babel compile. Each IIFE now defines its own variables.
- **Backward-compatible shared URLs:** old `monthlyPayment` query param maps to both `rentBudget` and `buyBudget`.
- **Budget-first closed-form formula explored, reverted:** `P&I = (B − D×m) / (1 + k×m)` validated but reverted — same total budget produced different derived home prices across locations, which confused testers. Preserved in research doc for future v4 consideration.

---

## v3.9.97 — April 2026

### Bug Fixes, CSS Touch-Ups, Chart Confirmation
- **Sensitivity chart bar color/label alignment fixed:** bars now colored by winner (blue = buying wins, green = renting wins) independently per side, not by direction of change. Previous logic let the "same" variable flip colors mid-chart depending on whether its ± delta favored buying or renting.
- **PMI consistency across buyer callouts:** two separate places rendered buyer's true monthly cost — one included PMI, one didn't. Unified both to include PMI estimate.
- **Chart consolidation from v3.9.96 validated:** 4-chart dropdown structure stable. No regressions from demoting the previous chart types.
- **CSS minor touch-ups:** slider thumb glow, input border hover states, panel header borderRadius.

---

## v3.9.96 — April 2026

### UX Updates & Chart Consolidation
- **Charts consolidated to 4:** Net Worth (8-line combined buyer/renter across custom, scenario, and both locations), Sensitivity Chart, Annual Cost, Equity & Investments (combined home equity + investment portfolios). Removed separate buyer/renter, advantage, equity-only, and portfolio-only charts.
- **Sensitivity chart redesigned:** Shows resulting verdict (winner + ROI% + $) on each side of every bar. Color indicates direction (blue=buying wins, green=renting wins) independently per side. ±delta prominently displayed. Hover on bars for full details. Negative always left, positive always right.
- **Home value callout simplified:** Removed editable down payment % input (caused confusion showing two conflicting home prices). Now shows single engine-derived home price with all insights centered.
- **Callout CSS centered:** All text in home value callout now consistently centered.
- **Sell year hint updated:** "5+ yr to recoup costs" → "Median US tenure: 10-13 yr" (NAR 2024).

---

## v3.9.95 — April 2026

### ROI, CAGR & UX Updates
- **ROI verdict display:** Verdict now shows both sides independently — "Buying: $523K total wealth · 468% ROI (12.3%/yr)" and "Renting: $704K total wealth · 665% ROI (14.5%/yr)". Winner by simple ROI subtraction. Dollar advantage demoted to supporting text.
- **Investment Discipline renamed:** "Renter Discipline" → "Investment Discipline" across all files. TIPS tooltip updated to clarify it applies to both sides' surplus. Reflects Bernstein-Koudijs asymmetry: mortgage = forced savings, renter surplus = voluntary.
- **Buyer cost breakdown:** New summary between inputs and callout: "Rent = $2,000/mo. Buyer's true cost = ~$2,775/mo (P&I + tax + ins + maint). Renter keeps the $775/mo surplus + $92,407 upfront invested."
- **First break-even:** All break-even labels now say "First break-even" to clarify multiple crossover possibility.
- **Break-even sensitivity clarified:** "(appr ±1%: 7/3)" → "(if appr. 3.0%: yr 7 · 5.0%: yr 3)".
- **Sim length default:** Changed from fixed 30 years to mortgage term + 5 (35 for US). Charts always show post-payoff inflection point.
- **Confidence text on own line:** 🟢🟡🔴 explanations wrap to separate line below icon+text.
- **Verdict disclaimer centered.**
- **NaN bug fixed:** Callout referenced wrong property names (propTax→propTaxRate, insurance→insRate, maintenance→maintRate).

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
- `rent-vs-buy-sim-audit.md` — Formula-by-formula math verification, 23 items reviewed

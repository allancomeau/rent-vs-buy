# v4.0 Scoping — Beginner-Mode Three-Budget Architecture

**Status:** Scoped, pre-implementation.
**Scope discipline:** v4.0 is the three-budget input rework + Mo.Investments derived readout. Nothing else. Expert mode, Mo.Investments unlock, extra payments, prepay callout, `otherItemized` / `renterIns` disposition — all deferred to v4.1+ per established slicing pattern.

---

## Why this version earns the major bump

Every v3.9.99.N version preserved engine semantics for existing inputs. A user's shared link from v3.9.99.12 opening in v3.9.99.15 still meant the same thing. That safety property was what let us ship silently between sessions and patch-level rollback without semantic regression.

v4.0 breaks that contract deliberately:

1. **`buyBudget` semantics flip.** v3.x: "Monthly P&I only." v4.0: "Monthly all-in housing cost (P&I + tax + ins + maint + PMI)."
2. **`downPayment` semantics expand.** v3.x: "Cash toward purchase; closing costs added silently by engine on top." v4.0: "Total day-0 deployment, inclusive of closing costs and fees."
3. **Home price derivation changes shape.** v3.x closed-form solved from P&I + down. v4.0 closed-form solves from three simultaneous constraints (Buy Budget monthly, Savings Budget day-0, metro rates) with piecewise PMI.
4. **Same headline input produces different home price.** Example: $2,000/mo at US defaults → ~$334K (v3.x) vs ~$250K (v4.0). Correct behavior in both cases for their respective semantics, but a user seeing their bookmarked scenario shift would rightly expect a version signal.
5. **URL params get renamed with migration path.** Shared-link discoverability feature needs explicit versioning to avoid silent breakage.

A .99.N patch with these properties would violate the trust contract the rest of the sequence established.

---

## The input model (beginner mode, v4.0)

**Six visible inputs**, none linked to each other by default:

| # | Input | Semantics | Default |
|---|---|---|---|
| 1 | **Rent Budget** | Monthly rent outflow. Standalone, no link. | Metro-derived |
| 2 | **Buy Budget** | Monthly all-in housing cost (P&I + tax + ins + maint + PMI). Standalone. | Metro-derived |
| 3 | **Savings Budget** | Total day-0 cash deployment (down + closing + fees). Standalone. | Metro-derived |
| 4 | **Mortgage Term** | Years of mortgage. Unchanged from v3. | 30 (metro default) |
| 5 | **Holding Period** | Year at which you'd sell. Unchanged. | 10 |
| 6 | **Investment Discipline** | % of implied surplus actually invested. Unchanged. | 65% |

**Default metro on fresh load: Boston, USA.** No fabricated starting numbers — all three budget defaults come from Boston's metro data (rent from rent index, Buy Budget reverse-computed from a plausible starter-home price at the metro's tax/ins/maint rates, Savings Budget as ~20% down + closing on that price). This ensures the default state always passes sanity checks regardless of future data refreshes.

**Sim Length** (how many simulation years to render) stays where it lives today — in the chart card, not in Your Situation. Not counted in the visible-inputs total.

**Mo.Investments** is a NEW **derived readout**, not an input in v4.0. Renders as a full input-field-shaped control beside Investment Discipline, showing `max(0, |Buy − Rent|) × Discipline`. Whichever side has the lower monthly housing cost has the surplus; the field shows what gets invested monthly after Discipline is applied.

**v4.0 rendering:** input-shaped control, **disabled state** (greyed, matching the existing 🔒-locked field styling used elsewhere), auto-populated with the derived value, updates live as Buy/Rent/Discipline change. A lock icon (🔒) indicates the field will become editable in a future version. Hover/tap on the lock shows "Direct input coming in v4.1" or equivalent.

**Rendering this as a disabled input (not a slider annotation or separate card) is a deliberate choice** — it makes the v4.1 "unlock" transition a state flip (disabled → enabled, 🔒 → 🔓), not a layout reshuffle. The user sees the intended shape of the control from day one; v4.1 just lets them override it.

**Deleted from UI** (vs. v3.9.99.15):
- Mortgage P&I (folded into Buy Budget)
- Rent Payment ↔ Mortgage P&I 🔗 link toggle (beginner mode has no link — pair is conceptually independent now; link toggle returns in Expert mode per v4.1+)

**Renamed** (same semantics, clearer names):
- Rent Payment → **Rent Budget**
- Down Payment → **Savings Budget** (semantics expanded — see above)

---

## The math

### Closed-form home-price solve

Let:
- `H` = home price (unknown)
- `B` = Buy Budget (monthly, all-in)
- `S` = Savings Budget (day-0 total)
- `c` = closing-cost fraction of home price (metro default, ~2-3%)
- `t` = annual property tax rate
- `i` = annual insurance rate
- `m` = annual maintenance rate
- `r` = monthly mortgage rate (annual / 12)
- `n` = mortgage term in months
- `A` = amortization factor = `r(1+r)^n / ((1+r)^n − 1)`, such that P&I = `loan × A`
- `p` = annual PMI rate (conditional on LTV, see below)

**Day-0 constraint:**
```
S = downPayment + closingCosts
S = (H − loan) + c·H
loan = H·(1 + c) − S
```

**Monthly constraint:**
```
B = P&I + tax_mo + ins_mo + maint_mo + PMI_mo
B = loan·A + H·(t + i + m)/12 + PMI_mo
```

**Substitute and solve for H:**
```
H = (B − PMI_mo + S·A) / [(1 + c)·A + (t + i + m)/12]
```

### PMI as a piecewise solve

PMI is a **4-tier schedule** based on LTV (loan-to-value), preserved from v3.x:

| Down payment % | LTV | PMI rate (annual, % of loan) |
|---|---|---|
| ≥ 20% | ≤ 80% | 0% (no PMI) |
| 15–20% | 80–85% | 0.2% |
| 10–15% | 85–90% | 0.3% |
| 5–10% | 90–95% | 0.5% |
| < 5% | > 95% | (engine handles; minimum down enforced upstream) |

Using the day-0 constraint `LTV = (1+c) − S/H`, the tier thresholds collapse to checks on `S/H` against known constants (adjusted by `c`, the closing-cost fraction). E.g., at `c = 0.03`:

| PMI rate | `S/H` threshold |
|---|---|
| 0% | ≥ 0.23 |
| 0.2% | 0.18–0.23 |
| 0.3% | 0.13–0.18 |
| 0.5% | 0.08–0.13 |

**Four cases, solve each, pick the self-consistent one:**

For each PMI rate `p ∈ {0, 0.002, 0.003, 0.005}`:
1. Substitute `PMI_mo = loan·p/12 = (H·(1+c) − S)·p/12` into the monthly constraint
2. Solve resulting linear equation for H (the `H·(1+c)·p/12` term moves to denominator side)
3. Accept the solution if the resulting `S/H` falls inside that tier's band

Exactly one case is self-consistent for any valid input — the four tiers partition the `S/H` space, and the solve's internal consistency requirement picks the matching tier. No iteration needed.

**Open detail for implementation:** PMI is conventionally quoted as annual % of outstanding loan balance (declining over amortization) vs. original loan (flat until natural 80% LTV dropoff). At t=0 both expressions equal `original_loan · p / 12`, so the solve is identical. The declining-vs-flat choice only matters inside the year-by-year `simulate()` loop. **Use declining-balance per v3.9.99.8 precedent.**

### Engine deltas summary

The closed-form solve is new. Everything downstream is unchanged:
- `simulate()` — unchanged. Takes a params object with `homePrice`, `loanAmount`, etc. The new solve just produces those values differently.
- `backsolveLoan()` — retained for backward compatibility during URL migration (legacy `buyBudget` value treated as P&I, backsolveLoan used to estimate loan, derive home price, then re-solve the v4 way with the estimate as starting point).
- Amortization, tax-benefit, sale-proceeds, discipline application, investment compounding — all unchanged.

**Engine bug fixes from v3.0 preserved** (final P&I uses `mi + mp` not full `monthlyPmt`; selling costs reduce capital gain per IRS Pub 523).

---

## UI layout changes in "Your Situation"

**Current (v3.9.99.15) Your Situation has:**
- Rent Payment + Mortgage P&I (linked by default with 🔗 toggle)
- Savings / Down Payment
- Mortgage Term, Holding Period, Investment Discipline
- Location section (below 2px border-top)
- Financial snapshot callout with disclosure (PMI estimate, income check, transaction costs, home price)

**v4.0 Your Situation:**
- **Rent Budget** (standalone)
- **Buy Budget** (standalone, all-in)
- **Savings Budget** (standalone, day-0 total)
- **Mortgage Term, Holding Period**
- **Investment Discipline** + **Mo.Investments** derived readout rendered inline next to the slider
- Location section — unchanged
- Financial snapshot callout — restructured:
  - "Supported home price: $X" — now the headline, not a derived consequence
  - "Required day-0: $Y down + $Z closing = $W total" (compare to Savings Budget; they match by construction)
  - "Monthly breakdown: $P P&I + $T tax + $I ins + $M maint + $PMI PMI = $B total" (compare to Buy Budget; they match by construction)
  - "Income check at 28% front-end ratio" — unchanged
  - "Round-trip transaction costs" — unchanged
  - PMI promotes out of details (it's now engine-level, not display-only)

The callout's role shifts from "here's what you're committed to beyond what you typed" to "here's what your three numbers add up to." Transparency, not surprise.

---

## URL migration

**Param renames:**

| v3.x | v4.0 | Legacy-load behavior |
|---|---|---|
| `rentBudget` | `rentBudget` | Unchanged semantics. No migration needed. |
| `buyBudget` | `buyAllIn` | If legacy `buyBudget` present and `buyAllIn` absent, treat legacy value as P&I. Estimate all-in using the metro's default tax/ins/maint rates applied to the home price the legacy value would have implied. Show inline migration notice. |
| `downPayment` | `savings` | If legacy `downPayment` present and `savings` absent, estimate closing at metro-default `c` and set `savings = downPayment + closingEstimate`. Show inline migration notice. |

**Migration notice UX:**
Inline, non-modal, single line above Your Situation:
> "This shared link uses a v3 format. We've estimated your all-in buy cost and total savings from the old values. Adjust as needed."

Dismissible via a ✕ button. Dismisses on first interaction with any Your Situation input as well, so users who just start editing never see the banner linger.

Migration runs once on URL load, not on every render. State reflects the migrated values from that point forward.

---

## Amber warnings

### Existing amber warnings — what survives

- **Outside typical range** warnings on mortgage term (<15 or >30), holding period (<3 or >30), discipline (<40% or >80%) — preserved.
- **Rent ≠ Buy apples-to-apples** warning — **deleted.** Rent Budget and Buy Budget are conceptually independent in v4.0; there's no "apples-to-apples" baseline to warn against. In Expert mode (v4.1+), the link toggle and its >10% delta warning return.

### New amber warnings — Mo.Investments distortion detection (v4.0 applies)

**Key insight:** even with Mo.Investments as a derived readout (not yet user-editable), the user can still drive the investing math by adjusting Rent Budget or Buy Budget independently. Lowering Rent Budget while holding Buy Budget steady inflates the implied Mo.Investments → renter's path gets a huge compounded boost over the holding period. The amber logic needs to catch these indirect-distortion cases in v4.0, not just the direct-override cases that become possible in v4.1.

Two distinct amber cases, both applicable in v4.0:

**Type 1 — "Investing setup is driving the advantage"**
Fires when the Mo.Investments stream is the dominant factor in the verdict, regardless of direction. Copy spirit: "Your monthly investment differential ($X/mo) compounds to ~$Y over your holding period — that's the main driver of this verdict, not the housing math itself." Useful signal, but lower-priority than Type 2.

**Type 2 — "Setup favors one side, verdict goes the other way"** (priority case)
Fires when the Mo.Investments setup would naively favor [X] but the final verdict goes [Y]. Copy spirit: "Even though your budget setup has the [renter/buyer] investing $X/mo more, your [appreciation rate / tax treatment / holding period / leverage] pushes the overall verdict toward [buying/renting]."

Type 2 is the priority case. Driven by real user feedback: users want to understand how housing can compete on quality-of-life even when the monthly-investing math looks stacked against it. The amber makes this tension legible rather than hiding it.

**Trigger threshold (decide-during-build):** Amber fires when projecting the monthly investing gap at the engine's investment-return rate over the holding period would have predicted the opposite verdict direction by a meaningful margin (suggested: >10% of total wealth at sell year). Tight enough to not fire on trivial distortions; loose enough to catch the user-feedback case. Refine during implementation.

**v4.1 extension:** When Mo.Investments unlocks to direct user input, both amber types continue to apply — the user has a new direct lever, but the detection logic is the same. No additional amber type needed in v4.1; the existing Type 1 + Type 2 just trigger via the new direct path in addition to the indirect path.

---

## What does NOT change in v4.0

- **The engine core.** `simulate()`, amortization, tax-benefit calc, sale-proceeds, discipline application — untouched.
- **Location / metro / country data.** 16 countries, 54 metros — unchanged.
- **Charts.** All four (Input Sensitivity, Net Worth, Cash Outflow, Balances) — unchanged.
- **Your Assumptions + What-If panels.** Unchanged.
- **Glossary.** Existing entries (15 table + 7 input) unchanged. See "Glossary additions needed" below for what v4.0 adds.
- **Themes, FX, tutorial, SEO, print stylesheet.** Unchanged.
- **Engine internals for `otherItemized`, `renterIns`.** Left at their current state (zeroed, preserved architecturally). Final disposition in v4.3.

---

## Glossary additions needed

Four new entries folded into the existing **Assumption Input Definitions** subsection (not a new subsection — keeps the Glossary tab clean):

| term | purpose |
|---|---|
| **Rent Budget** | Defines what "rent" means in v4, flags that it's standalone (no apples-to-apples link in beginner mode), notes it's user's real rent scenario or hypothetical |
| **Buy Budget** | Defines all-in semantics (P&I + tax + ins + maint + PMI), contrasts with v3 P&I-only semantics, explains the engine splits the budget into components using metro rates |
| **Savings Budget** | Defines day-0 total deployment (down + closing), explains buyer spends it on upfront costs and renter invests it — same starting capital, different deployment |
| **Mo.Investments** | Derived readout in v4.0. Formula `max(0, \|Buy − Rent\|) × Discipline`. Notes that whichever side has lower monthly housing cost has the surplus. Flags that v4.1 will unlock direct input. |

**Explicit scope decision: DO NOT migrate the Your Situation input `<Tip>` tooltips to `<GlossaryLink>` in v4.0.** The input *structure* itself is the v4.0 change, and stacking a tooltip-wrapper migration on top would conflate two changes in one version. Completing the tooltip migration arc (started in v3.9.99.12 and v3.9.99.15) is a clean follow-up version — likely v4.0.1 or v4.1. Entries are seeded now so that future migration is drop-in.

---

## Test-pass plan (blocking for v4.0 ship)

The v3.9.99 budget-first attempt failed on one specific tester reaction: "same budget → different home price per location." The new framing defuses this (total budget naturally varies by location), but one fresh-tester walk-through is required before commit.

**Minimum pre-ship validation:**

1. **Default-load sanity:** Fresh load with US defaults produces a plausible home price in realistic metros. Rough expected range: $250K-$400K depending on metro. No NaN, no negatives, no >$2M outputs.
2. **PMI edge cases:**
   - `S = 0.2 · H` exactly → should hit the no-PMI branch (boundary inclusive).
   - `S = 0.1 · H` → should hit PMI branch, PMI shows in monthly breakdown.
   - Very high `S` (70% down) → no PMI, very low loan, home price driven mostly by Buy Budget.
   - Very low `S` ($5K) → high PMI, smaller home, engine should still solve cleanly.
3. **Cross-metro consistency:** Same three budget values, switch metros → home price varies as expected, all three components (down, closing, monthly breakdown) reflect the metro's rates.
4. **URL migration:** Open a v3.x shared link → migration notice shows, inputs populated with estimated values, can be dismissed, old-vs-new home-price delta is disclosed inline in the notice.
5. **Discipline slider + Mo.Investments readout:** Slide discipline 0% → 100%, Mo.Investments readout updates live. At discipline = 0, readout shows $0. At 100%, shows full `|Buy − Rent|`.
6. **Mortgage term / holding period:** Changing mortgage term changes `A`, changes home price. Changing holding period doesn't change home price (it only affects simulate() projections).
7. **Fresh-tester walk-through** (the one that killed v3.9.99): Give someone unfamiliar the tool at default. Does the mental model "tell me what I can afford monthly + how much I've saved, and you tell me what house that is" land without confusion? Does the "different home price per metro with same budgets" phenomenon register as intuitive (because tax/ins/maint are different) rather than confusing?

Test 7 is the one that actually failed last time. Don't skip it.

---

## Version boundary consequences

### What happens on ship

- Footer bumps to **v4.0** (not v3.9.99.20 — this is the whole point of the version discussion).
- CHANGELOG gets a `## v4.0` section. This is the first non-.99 entry in the v3→v4 arc.
- PROJECT_INSTRUCTIONS updates:
  - Current State header: `## Current State: v4.0 — Three-Budget Architecture`
  - Versioning table: new section header for v4.x above the v3.x table, v4.0 row added
  - Pending Items: v4.0 section collapses to completed items; v4.1+ pending items surface as the next active scope
- `v4_0_scoping.md` archives: either deleted or moved to a `/archive` subpath. Don't leave a stale scoping doc for a shipped version cluttering the project-files list.

### What doesn't happen

- **No URL-param forced migration.** Legacy v3 links continue to work via the migration notice. Migration is display-layer, not state-model — no forced redirect, no server-side anything.
- **No engine version gate.** `simulate()` is unchanged; there's no "v3 engine vs v4 engine" branching inside the core math.
- **No feature-flag for v3 layout.** We commit to v4 on ship. No "revert to v3 layout" toggle. If v4 lands badly, we roll back the whole version — consistent with the trust contract.

---

## Implementation order when build begins

Suggested execution sequence — each step independently verifiable before the next:

1. **Closed-form solve function** in isolation. Pure function, testable without UI. Confirm test cases (PMI 4-tier boundaries, cross-metro, default-load sanity) return sane numbers. Building this as a pure function first decouples math correctness from UI correctness — when a number looks wrong after full wire-up, we know instantly whether the solve is wrong or the wire-up is wrong. The v3.9.99 budget-first attempt coupled math + UI + wiring in one pass and debugging became a three-way attribution problem; this sequencing is specifically designed to avoid that.
2. **Engine integration.** Replace `buildParams` with v4 semantics — not branch to `buildParamsV4`. Major-version bump means v3 input state never exists in memory post-migration. `backsolveLoan` is preserved but its only remaining caller is the URL-migration handler (step 7), not the main engine path. simulate() unchanged.
3. **Input UI.** Rename fields, remove link toggle, remove P&I input. Do NOT wire new engine yet — UI just sets state.
4. **Engine wire-up.** Connect v4 inputs → v4 solve → simulate(). Verify default-load renders correctly.
5. **Financial-snapshot callout rewrite.** Repoint to derived home price + day-0 breakdown + monthly breakdown.
6. **Mo.Investments derived readout.** Render next to Discipline slider. Read-only.
7. **URL migration.** `buyAllIn` / `savings` param support, legacy-load handler, migration notice component.
8. **Glossary entries.** 3-4 new entries per list above.
9. **Amber cleanup.** Delete the Rent/Buy apples-to-apples warning and its associated state.
10. **Verification pass + fresh-tester walk-through.**
11. **Version bump, CHANGELOG, PROJECT_INSTRUCTIONS update, scoping-doc archive.**

Expect this to span multiple sessions. Each numbered step is a clean stopping point for session handoff.

---

## Open items for v4.0 implementation (decide during build)

Not blocking for scoping; flagged for the implementer:

1. **Amber Type 1 vs Type 2 trigger thresholds** — the "meaningful magnitude" benchmark for firing the Mo.Investments distortion amber. Suggested >10% of total wealth at sell year; refine after observing real usage.
2. **Boston metro default values** — confirm during implementation that Boston's metro data produces plausible Rent Budget / Buy Budget / Savings Budget defaults via reverse-compute. No fabricated numbers.
3. **Lock icon copy** — exact wording for the hover/tap on the v4.0 disabled Mo.Investments lock: "Direct input coming in v4.1" or equivalent. Minor.
4. **Migration notice timing** — dismisses on ✕ click or first interaction with any Your Situation input, whichever comes first. Implementation detail.

---

## Scope guardrails (what v4.0 is NOT)

If these come up during implementation, they're v4.1+ work, not v4.0:

- Expert mode toggle and all its contents
- Mo.Investments unlock + direct input + inflation-adjust
- Extra monthly mortgage payment
- Prepay vs. invest callout
- `otherItemized` engine removal or surface
- `renterIns` disposition
- Expert-mode clamp relaxation
- Any change to charts, Location section, Your Assumptions panel, What-If panel, themes, FAQ, Methodology content

If a "while we're in there" temptation surfaces, say it out loud to me before editing. Scope creep is how v3.9.99.x got to v3.9.99.15.

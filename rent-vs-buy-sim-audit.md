# Mathematical verification and bug audit of a rent-vs-buy simulation engine

This simulation engine is **fundamentally sound in its core financial mathematics** but contains **two confirmed bugs and one critical design limitation** that affect output accuracy. The amortization formula, backsolve function, tax benefit method, and portfolio mechanics all pass verification against authoritative sources (CFPB, IRS, Poterba 1984). However, the capital gains calculation on home sale violates IRS Publication 523 rules, the final mortgage payment overstates buyer costs in the payoff year, and the `startRent = monthlyPmt` design breaks entirely for high-down-payment scenarios. The engine's modeling choices — property tax lag, inflation-based insurance, Poterba-style maintenance — are well-grounded in housing economics literature, though several represent deliberate simplifications worth documenting.

---

# Part 1: Formula-by-formula math verification

## 1. Amortization and backsolve formulas are textbook-correct

**Standard payment formula: CORRECT.** The implementation `M = P × [r(1+r)^n] / [(1+r)^n − 1]` is the universally accepted fixed-rate mortgage payment formula, confirmed by the CFPB, Wikipedia's amortization derivation via geometric series, Wall Street Prep, OpenStax textbooks, and every major lender (Chase, Rocket Mortgage, Bankrate). The derivation models loan balance as a recursion `B_{k+1} = B_k(1+r) − M` with terminal condition `B_n = 0`, solved via present value of an ordinary annuity.

**Backsolve formula: CORRECT.** The formula `P = M × [(1+r)^n − 1] / [r × (1+r)^n]` is the algebraic inverse of the payment formula — equivalently, the Present Value of an Ordinary Annuity formula `PV = PMT × [1 − (1+i)^(−n)] / i`. Microsoft Excel's `PV()` function uses this identical relationship.

**Zero-rate edge case: CORRECT.** When `r = 0`, the code returns `M × n` for backsolve and `P / n` for payment. Wikipedia's amortization article explicitly states: "If i = 0 then simply A = P/n." This is also the L'Hôpital limit of the standard formula as r → 0. The explicit branch is necessary because the standard formula evaluates to 0/0 at r = 0.

## 2. Month-by-month amortization loop matches bank methodology

The inner loop computes `interest = balance × mr`, `principal = min(payment − interest, balance)`, `balance = max(0, balance − principal)`. This is **the standard bank amortization method**, confirmed by the CFPB's own open-source amortize module, Bankrate's amortization calculator, and the reference `amortize.js` implementation. The `Math.min` guard on principal and `Math.max` guard on balance are standard safeguards against floating-point overshoot in the final payment — functionally equivalent to how banks adjust the last payment to exactly zero the balance.

One subtlety: in the final payoff month, the **actual payment** should be `interest + remaining_balance`, which is less than `monthlyPmt`. The guards correctly handle the balance, but `yrMtgPmt` still accumulates the full `monthlyPmt` for that month. This is a confirmed bug addressed in Part 2.

## 3. Property tax on previous year's value is a defensible convention

The code uses `prevHomeVal × propTaxRate`, creating a **one-year assessment lag**. This is a **reasonable and slightly conservative** modeling choice. Property tax assessments structurally lag market values: most jurisdictions reassess on 1–5 year cycles, and many states impose statutory caps on annual increases (California's Prop 13 limits increases to 2%/year; Florida caps at 3%). A peer-reviewed study in *The Journal of Real Estate Finance and Economics* (2023) found assessment uniformity drops 3–6% with each additional year of revaluation delay. The one-year lag actually **understates** the true lag in many jurisdictions. For year 1, using the purchase price is correct — upon change of ownership, most jurisdictions reassess to the sale price.

## 4. Insurance on purchase price grown by inflation is well-justified

The formula `homePrice × insRate × (1 + inflation)^(yr−1)` bases insurance on the original purchase price inflated annually, **not** on current market value. This is **the correct approach**. Homeowner's insurance premiums are based on **replacement cost** (the cost to rebuild), not market value. Policygenius, State Farm, Allstate, and TruStage all confirm this. Replacement cost excludes land value and tracks construction costs (labor + materials), which correlate with general inflation rather than speculative home appreciation. The American Family Insurance company explicitly states it "references the Consumer Price Index to measure inflation and adjusts rates accordingly." Many insurers offer "inflation guard endorsements" that automatically adjust coverage with construction cost inflation. If anything, recent years have seen premiums outpace inflation by **8.7%** (per the Treasury Federal Insurance Office, 2018–2022) due to climate events — so this approach may slightly underestimate.

## 5. Maintenance on current home value follows Poterba's δ directly

The formula `homeVal × maintRate` applies a fixed percentage to current home value, directly mirroring **Poterba's (1984) depreciation rate δ** in the user cost of housing model: `User Cost = P × [(1−τ)(r + τp) + δ − π]`. Keane & Liu (ASU/Duke) confirm "depreciation and maintenance are proportional to house value" is an explicit assumption in the Poterba framework. Academic estimates for δ range from **1% to 2.5%**: Poterba (1992) uses 2% for owner-occupied property, Himmelberg, Mayer & Sinai (2005) use 2.5%, and Ben Felix uses 1%. This is the standard academic approach.

## 6. The excess itemization method is the academically correct post-TCJA approach

The formula `max(0, itemized − stdDeduction) × marginalRate` computes the **marginal** tax benefit of homeownership — only the amount by which itemized deductions exceed the standard deduction matters. This is **the correct economic methodology**, validated by multiple academic sources. A 2022 ScienceDirect study found "only 27% of mortgage interest and property taxes are above the standard deduction amount," confirming that treating the full deduction as a subsidy is wrong. An NBER working paper (Glaeser & Shapiro 2003) explicitly warns that "ignoring this non-linearity yields significantly higher estimates of subsidies." Post-TCJA, ~90% of taxpayers take the standard deduction, making this distinction critical.

**The SALT cap implementation `min(propTax, saltCap)` is a reasonable simplification** but slightly **overstates** the tax benefit. The actual SALT cap applies to **all** state and local taxes combined (income + property + sales), not just property tax. A taxpayer paying $8,000 in state income tax and $6,000 in property tax has only $2,000–$4,000 of property tax room under the cap, not the full $6,000 the code would allow. The correct implementation would be `min(stateIncomeTax + propTax, saltCap)`, but since a rent-vs-buy tool doesn't model state income taxes (they're identical whether renting or buying), this is an acceptable shortcut.

**Important update**: The SALT cap was raised from **$10,000 to $40,000** for tax years 2025–2029 under the One Big Beautiful Bill Act (signed July 4, 2025), with phase-down for MAGI above $500,000. The code uses a configurable `p.saltCap` parameter, which is good design — users can update this value.

## 7. Capital gains on home sale has a confirmed error

The code computes `homeGain = max(0, homeVal − homePrice)` then `homeTax = max(0, homeGain − capGainsExclusion) × capGainsRate`. The Section 121 exclusion as a simple subtraction from gain is **correct** per IRC § 121(a)–(b).

However, **selling costs are not subtracted from the gain, which violates IRS Publication 523**. The publication's Worksheet 2 explicitly defines: `Amount Realized = Selling Price − Selling Expenses`, then `Gain = Amount Realized − Adjusted Basis`. The correct formula should be `homeGain = max(0, homeVal − sellCosts − homePrice)`. Because the code separately subtracts `sellCosts` from `buyerWealth` but doesn't reduce the gain by `sellCosts`, it **double-penalizes selling costs** in the tax calculation. For a $500K home appreciating at 3%/year over 30 years (selling at ~$1.21M), this overstates capital gains tax by approximately **$10,900** (the product of selling costs and the capital gains rate). This is addressed as a confirmed bug in Part 2.

## 8. Investment capital gains approach is correct with known simplifications

The formula `(portfolio − basis) × capGainsRate` correctly uses cost basis per IRS Topic No. 409. Assuming all gains are long-term capital gains is **reasonable** for a multi-year simulation where assets are held for years. The main simplification is that **dividends are taxed annually** in reality, not deferred until liquidation. This understates the ongoing tax drag on investments, slightly biasing results toward renting. The 3.8% Net Investment Income Tax (NIIT) for high-income taxpayers is also omitted. These are acceptable simplifications for a planning tool.

## 9. Renter portfolio seeded with down payment + closing costs is standard

Seeding the renter's portfolio with `downPayment + closingCosts` is **the standard approach** used by virtually all major rent-vs-buy models. Nick Maggiulli's "Of Dollars and Data" calculator explicitly states the starting portfolio "includes your closing costs and down payment." The NYT Rent vs Buy calculator presumes "investing both the down payment, and the presumed difference between house cost minus renting cost." Beracha & Johnson (2012) include buying expenses in the opportunity cost. NerdWallet, BuyRentLab, and Fidelity calculators all use this same seeding approach.

## 10–11. Portfolio growth timing and surplus investing are reasonable conventions

The code applies `portfolio *= (1 + investReturn)` **before** adding new contributions, meaning new savings don't earn returns until the following year. This is an **end-of-year contribution convention**. The alternative (contributions before growth) would be a beginning-of-year convention. Neither is perfectly correct — real contributions happen monthly throughout the year, so the true answer lies between these conventions. A mid-year approximation would be most accurate, but the end-of-year convention is conservative and standard in simplified annual models. The **symmetric surplus investing** approach (both sides invest their cost advantage) is well-established in the literature, used by the NYT calculator, Maggiulli, and Beracha & Johnson.

## 12. Starting rent = mortgage payment is practical but not academic standard

Setting `startRent = monthlyPmt` answers the question "given a fixed monthly budget, which is better?" This has intuitive appeal but is **not the academic standard**. Ben Felix's 5% rule sets breakeven rent = 5% × homePrice / 12 (based on unrecoverable ownership costs). The NYT calculator takes actual local rent as a user input. Beracha & Johnson use observed market rent-to-price ratios. Felix explicitly criticizes the rent-equals-payment comparison: "It's often assumed, if you can purchase a home with a mortgage payment that's equal to or less than what you would otherwise pay in rent, then home ownership is the way to go. Unfortunately, this is a mathematically flawed way to think about it." The code's approach is a defensible heuristic but introduces a design flaw when `downPct` is high (see Part 2).

---

# Part 2: Bug and edge-case audit

## Two confirmed bugs that affect numerical accuracy

**Bug 1: Final mortgage payment overstates buyer costs (Severity: Medium).** In the payoff month, when the remaining balance is small (e.g., $500), the code adds the full `monthlyPmt` (e.g., $2,000) to `yrMtgPmt`, even though the actual payment is only `interest + remaining_balance` (e.g., ~$502). The `Math.min` guard correctly prevents the balance from going negative, but `yrMtgPmt` records a phantom ~$1,498 overpayment. This inflates `totalBuyerCost`, `netBuyerCost`, and `totalBuyerUnrecoverable` in the payoff year. It also distorts the `diff` variable, affecting portfolio allocations. For a standard 30-year mortgage in a 30-year simulation, only the final year is affected, but for shorter terms or longer simulations, the error compounds through incorrect portfolio contributions.

**Fix:** Replace `yrMtgPmt += monthlyPmt` with:
```javascript
const actualPmt = Math.min(monthlyPmt, mi + balance);
yrMtgPmt += actualPmt;
```

**Bug 2: Selling costs not subtracted from capital gain (Severity: Medium).** As detailed in Part 1 §7, the code computes `homeGain = max(0, homeVal − homePrice)` without deducting `sellCosts`. Per IRS Publication 523, selling expenses reduce the amount realized, which reduces the taxable gain. Because `sellCosts` are separately subtracted from `buyerWealth`, the code effectively double-penalizes them — the buyer pays the costs AND pays tax on the portion of gain those costs should have eliminated. For a home selling at $1.21M with 6% selling costs ($72.7K) and a single filer's $250K exclusion, this overstates tax by **~$10,900**.

**Fix:** Change the gain calculation to:
```javascript
const homeGain = Math.max(0, homeVal - sellCosts - p.homePrice);
```

## One critical design limitation in the rent comparison framework

**The `startRent = monthlyPmt` design breaks for high down payments (Severity: Critical).** When `downPct = 1.0` (all-cash purchase), `loan = 0` → `monthlyPmt = 0` → `startRent = 0`. The renter pays zero rent, making the entire comparison meaningless. Even at `downPct = 0.8`, the small loan produces an unrealistically low mortgage payment and thus unrealistically low rent. The NMHC (National Multifamily Housing Council) flagged this as one of the most common errors in rent-vs-buy calculators. Nearly all reputable calculators (NYT, NerdWallet, Fidelity, SmartAsset) take rent as an independent input.

**Recommended fix:** Accept `p.monthlyRent` as an independent parameter. Fall back to `monthlyPmt` only if no rent is provided.

## Edge cases that are handled correctly

**Zero mortgage rate** is cleanly handled. The ternary `loan > 0 && mr > 0` correctly routes to `loan / nPmts` for the zero-interest case, producing correct results. **Negative appreciation** does not crash the simulation — `homeGain` is floored at zero (no tax on losses), equity can correctly go negative (underwater mortgage), and maintenance costs decrease proportionally. **Tax benefit edge cases** all resolve correctly: `marginalTax = 0` produces zero benefit (international users), a large `stdDeduction` exceeding itemized deductions produces zero benefit via the `max(0, ...)` floor, and `capGainsExclusion = 999999999` correctly zeroes out home sale tax. **The discipline factor at 0%** correctly prevents new surplus contributions while still allowing returns on the initial portfolio seed. At 100%, all surplus is invested. **Year 1 timing** is correct across all cost categories — `(1 + growth)^0 = 1` produces the base values without growth.

## Potential issues worth monitoring

**`mortgageTerm = 0` causes division by zero.** When `mortgageTerm = 0` and `mr = 0`, the code computes `loan / 0 = Infinity`. When `mr > 0`, `Math.pow(1 + mr, 0) = 1`, giving a denominator of `(1 − 1) = 0`. Input validation should reject `mortgageTerm ≤ 0`.

**No PMI modeling.** When `downPct < 0.20`, lenders typically require Private Mortgage Insurance at 0.5–1.5% of the loan annually until 20% equity is reached. The code omits this, understating the cost of buying with low down payments. This is a common omission flagged by the NMHC.

**Closing costs not added to cost basis.** Under IRS rules, certain closing costs (title insurance, recording fees) can be added to the home's adjusted basis, reducing capital gains. The code uses `homePrice` as the basis, slightly overstating gains. The effect is small — typically a few hundred dollars in tax.

**Investment return timing is approximate.** New portfolio contributions don't earn returns until the following year (end-of-year convention). A mid-year approximation would be more accurate but the difference is modest for annual modeling.

**Negative interest rates** are treated as zero in `backsolveLoan` (`r ≤ 0` branch). This is acceptable for US-focused simulations but would be incorrect for European markets that experienced negative rates.

## Summary of all findings

| Item | Verdict | Severity |
|------|---------|----------|
| Amortization formula (M = P×r(1+r)^n / [(1+r)^n−1]) | ✅ Correct | — |
| Backsolve formula (P = M×[(1+r)^n−1] / [r(1+r)^n]) | ✅ Correct | — |
| Month-by-month amortization loop | ✅ Correct | — |
| Zero-rate edge case | ✅ Correct | — |
| Property tax on previous year's value | ✅ Reasonable | — |
| Insurance on purchase price × inflation | ✅ Well-justified | — |
| Maintenance on current home value (Poterba δ) | ✅ Standard | — |
| Excess itemization method | ✅ Correct | — |
| SALT cap on property tax only | ⚠️ Simplification (overstates benefit) | Low |
| Section 121 exclusion as subtraction | ✅ Correct | — |
| Investment capital gains (basis method) | ✅ Correct (simplified) | — |
| Renter portfolio seeded with down+closing | ✅ Standard | — |
| Symmetric surplus investing | ✅ Standard | — |
| Portfolio growth before contributions | ⚠️ Conservative approximation | Low |
| Discipline factor concept | ✅ Well-motivated | — |
| **Selling costs not reducing capital gain** | **❌ Bug** | **Medium** |
| **Final payment overstates yrMtgPmt** | **❌ Bug** | **Medium** |
| **startRent = monthlyPmt at high down payment** | **⚠️ Design flaw** | **Critical** |
| No PMI modeling | ⚠️ Omission | Low |
| mortgageTerm = 0 division by zero | ⚠️ Missing guard | Low |
| Closing costs not in cost basis | ⚠️ Simplification | Low |
| Annual dividend tax drag ignored | ⚠️ Simplification | Low |
| Rent = payment not academic standard | ⚠️ Design choice | Low |

The engine's core financial math is solid. Fixing the two confirmed bugs requires changing two lines of code. The `startRent` design limitation warrants adding an independent rent input parameter. All other items are documented simplifications consistent with the level of abstraction appropriate for a planning tool.
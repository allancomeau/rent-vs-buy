# The definitive financial models for rent vs. buy decisions

**The rent-vs-buy question is ultimately a comparison of two parallel wealth-accumulation paths**, and the best models in academia and industry converge on a consistent framework: compute the buyer's net equity at sale versus the renter's investment portfolio (seeded with the down payment and grown by monthly savings). The most important variables, ranked by sensitivity, are **home appreciation rate, time horizon, and mortgage rate** — together these three dominate the outcome. Research consistently finds breakeven timeframes of **5–7 years at moderate rates**, stretching to **7–11 years in the current 6.5%+ rate environment**, with real long-run home appreciation of only **~0–1% annually** (Shiller) making the investment case for homeownership rest primarily on leverage, imputed rent, tax benefits, and forced savings — not capital gains.

---

## The academic foundations: Shiller, Jordà, and Poterba

Three bodies of academic work form the intellectual bedrock for any serious rent-vs-buy model.

**Robert Shiller's long-run housing data** is the starting point. Using his repeat-sales Case-Shiller Index (1890–present), Shiller found that real U.S. home price appreciation was approximately **zero from 1890 to 1990** and roughly **0.4% per year** from 1890 to 2004. Even the more generous post-1940 measurement shows only **~0.7% real annual appreciation** — while homeowners systematically estimated 2%. Shiller's explanation: housing depreciates physically, goes out of style, and faces competition from new construction. The popular belief in ever-rising home values conflates nominal gains (driven by inflation) with real wealth creation.

**Jordà, Schularick, and Taylor's "The Rate of Return on Everything, 1870–2015"** (QJE 2019) offered a surprising counterpoint. Across 16 advanced economies, they found housing total returns averaged **~7% real per year** — on par with equities but with significantly lower volatility. The resolution of this apparent contradiction with Shiller is crucial: **~3.5–5% of that return is imputed rental yield** (the shelter value consumed by the owner-occupier), not liquid cash returns. For a rent-vs-buy model, the relevant decomposition is: capital gains (~1% real) plus the rent you don't pay, minus all ownership costs. Dimson, Marsh, and Staunton's Global Investment Returns Yearbook broadly confirms this: quality-adjusted real capital gains on housing have been approximately **−2% to +1%** since 1900, while global equities returned **~5.1% real**.

**James Poterba's user cost of housing model** (QJE 1984) provides the theoretical framework used by most academic analyses. The annual cost of owning $1 of housing is:

> **User Cost = P × [(1−τ)(i + τ_p) + m + δ − π_e]**

Where P = home price, τ = marginal tax rate, i = interest rate, τ_p = property tax rate, m = maintenance rate, δ = risk premium/depreciation, and π_e = expected appreciation. In equilibrium, this user cost should equal market rent. Himmelberg, Mayer, and Sinai (JEP 2005) extended this with an explicit loan-to-value decomposition:

> **Annual Cost = P × [r_f(1−τ)(1−LTV) + r_m(1−τ)(LTV) + τ_p(1−τ) + δ + m − g]**

This separates the opportunity cost of equity (r_f) from the cost of mortgage debt (r_m), accounts for tax deductibility, and subtracts expected capital gains (g). Their key finding: **real long-term interest rates are the single most important determinant** of whether buying makes sense, and house prices are more sensitive to rate changes when rates are already low — a crucial nonlinearity.

---

## The complete variable set and how models handle them

Every major model — the NYT calculator, Zillow's breakeven horizon, Fidelity, and academic user-cost frameworks — includes essentially the same variable set, though they differ in defaults and sophistication. The table below synthesizes the standard parameters with typical values:

| Category | Variable | Typical range | Notes |
|----------|----------|---------------|-------|
| Mortgage | Interest rate | 6.5–7.0% (2024–25) | Most impactful cost lever |
| Mortgage | Term | 30 years fixed | 15-year shifts amortization dramatically |
| Mortgage | Down payment | 9–20% | Below 20% triggers PMI |
| Mortgage | PMI rate | 0.3–1.5%/yr of loan | Removed at 80% LTV |
| Property | Property tax | 0.3–2.2% of value | Hawaii 0.31%, NJ 2.23% |
| Property | Maintenance | 1–2% of value/yr | Industry standard: 1% |
| Property | Insurance | 0.35–1.0% of value | Up 40% since 2019 |
| Property | HOA fees | $0–$500+/mo | Condo-specific |
| Transaction | Buy-side closing | 2–5% of price | Origination, title, appraisal |
| Transaction | Sell-side closing | 6–8% of sale price | Agent commissions + fees |
| Tax | Marginal rate | 22–37% | Determines deduction value |
| Tax | SALT cap | $10,000 | Post-TCJA; may change 2026 |
| Tax | Mortgage interest cap | $750,000 loan | Post-TCJA limit |
| Tax | Standard deduction | $15,000/$30,000 | Single/MFJ (2025 approx.) |
| Tax | Capital gains exclusion | $250K/$500K | Must own+live 2 of last 5 yrs |
| Growth | Home appreciation | 3–5% nominal (0–1% real) | Most sensitive variable |
| Growth | Rent growth | 2–4%/yr | Long-run avg ~3% |
| Growth | Investment return | 7–10% nominal | S&P 500 historical benchmark |
| Growth | Inflation | 2–3% | Drives nominal rates |

**How correlations are handled.** The honest answer: **most consumer calculators ignore correlations entirely.** The NYT calculator, Zillow, Fidelity, and NerdWallet all use deterministic, independent constant growth rates. Users can input implausible combinations (0% inflation with 5% rent growth). In reality, inflation drives both rent growth and home appreciation (Gallin 2004 showed house prices and rents are cointegrated long-run), and interest rates interact with home prices in complex ways. Academic user-cost models capture some of this through the inflation term in the Poterba equation — higher inflation simultaneously increases expected appreciation (π_e) and makes the after-tax mortgage cost cheaper in real terms. Monte Carlo approaches (used in institutional CRE but rarely in consumer tools) address this by sampling from correlated distributions, though parameterizing those correlations remains challenging.

---

## The math: month-by-month wealth simulation

There is **no closed-form solution** for the rent-vs-buy breakeven. The standard approach, used by every major calculator, is iterative month-by-month simulation comparing two wealth paths. Here are the exact formulas.

**Mortgage amortization.** The fixed monthly payment is:

> **M = P × [r(1+r)^n] / [(1+r)^n − 1]**

where P = loan principal, r = monthly rate (annual/12), n = total payments (years × 12). Each month k, interest is Balance_{k-1} × r, principal is M minus interest, and the new balance is the old balance minus principal paid. The remaining balance at payment k has a closed form:

> **Balance(k) = P × (1+r)^k − M × [(1+r)^k − 1] / r**

**Buyer's monthly outflow** at month k sums the mortgage payment plus property tax (home value × tax rate / 12), insurance (growing with inflation), maintenance (home value × rate / 12), HOA, and PMI (if LTV > 80%). Home value grows annually: Home_Value(t) = Price₀ × (1 + g)^t.

**Tax benefit calculation** uses the "excess itemization" approach — the only correct method:

> **Annual_Tax_Benefit = max(0, Itemized_Total − Standard_Deduction) × Marginal_Rate**

where Itemized_Total = deductible mortgage interest (on first $750K of debt) + min(property tax + state income tax, $10,000 SALT cap) + other itemized deductions. This means many homeowners, especially those with smaller mortgages, get **zero incremental tax benefit** because their itemized deductions don't exceed the now-large standard deduction.

**Renter's portfolio** starts with the down payment plus buy-side closing costs invested as a lump sum, then grows monthly:

> **Portfolio(k) = Portfolio(k−1) × (1 + r_inv) + Monthly_Savings(k)**

where r_inv = (1 + annual_return)^(1/12) − 1, and Monthly_Savings = Buyer_Monthly_Cost − Renter_Monthly_Cost (rent + renter's insurance). When buyer costs exceed renter costs, the renter invests the difference; when they don't, the renter draws down.

**Final wealth comparison** at year T:

> **Buyer_Wealth = Home_Value(T) − Balance(12T) − Selling_Costs − Capital_Gains_Tax_Home + Accumulated_Tax_Benefits**

> **Renter_Wealth = Portfolio(12T) − Capital_Gains_Tax_Investments**

Capital gains on the home: max(0, gain − exclusion) × cap gains rate. Capital gains on investments: (portfolio − cost basis) × cap gains rate. The breakeven year T* is when Buyer_Wealth first exceeds Renter_Wealth.

**For sensitivity analysis**, sophisticated models use Monte Carlo simulation with **1,000–10,000 runs**, drawing home appreciation from a normal/lognormal distribution (μ ≈ 3%, σ ≈ 6–8%), investment returns from a lognormal distribution (μ ≈ 8%, σ ≈ 15–18%), and rent growth from a normal distribution (μ ≈ 3%, σ ≈ 2%). Output is a probability distribution of wealth differences, reported as percentiles (P10/P50/P90). Cornell research found Monte Carlo produces expected NPV **$500,000 higher** than static DCF using the same average inputs, due to Jensen's inequality and the nonlinearity of leveraged returns.

---

## Ben Felix's 5% rule: elegant but rate-sensitive

Ben Felix's framework, introduced circa 2019, offers a powerful simplification. The core insight: compare **unrecoverable costs** of owning versus renting. Unrecoverable costs are expenses that produce no residual value — for renters, that's rent; for owners, it's property tax, maintenance, and the cost of capital.

The breakdown of the **~5% annual unrecoverable cost** of homeownership:

- **Property tax: ~1%** of home value (no residual value)
- **Maintenance: ~1%** of home value (preserves but doesn't increase value)
- **Cost of capital: ~3%** (calculated as a WACC-style blend)

The cost of capital component is the most intellectually interesting. Felix calculates it analogously to a **weighted average cost of capital**:

> **Cost of Capital = (% Debt × Cost of Debt) + (% Equity × Cost of Equity)**

With 20% down and 80% mortgage: Cost of Debt = mortgage rate (originally ~3%); Cost of Equity = expected stock return minus expected real estate return (Felix used 6.57% − 3% ≈ 3.57%, rounded to 3%). This yields 0.80 × 3% + 0.20 × 3% = **3.0%**. The elegant result: whether you pay all cash or finance heavily, the cost of capital stays around 3% — because financing substitutes explicit interest for opportunity cost.

**Application**: multiply home value by 5%, divide by 12 to get the monthly breakeven rent. A **$500,000 home** yields $25,000/year or **$2,083/month**. If comparable rent is below that, renting wins.

**Critical limitation in today's market**: Felix's original 3% mortgage assumption is outdated. At current **6.5% rates**, the cost of capital calculation becomes: 0.80 × 6.5% + 0.20 × 3% = **5.8%**, pushing total unrecoverable costs to roughly **7.8%** of home value. This means the "5% rule" should arguably be a "**6–8% rule**" in the current environment. Felix himself acknowledged the rule needs dynamic adjustment for rate changes. Other limitations: it's a point-in-time snapshot (doesn't capture rent escalation vs. fixed mortgage payments over time), excludes transaction costs and tax benefits, and assumes the renter actually invests all savings — a behavioral assumption contradicted by the data showing **median renter net worth of $6,300 vs. $255,000 for homeowners** (Federal Reserve SCF 2019).

---

## What the research concludes: sensitivity rankings and breakeven findings

Synthesizing across all sources, the variables that most influence the rent-vs-buy outcome, ranked by sensitivity:

1. **Home appreciation rate** — the single most powerful variable due to leverage amplification (5:1 with 20% down). A 1% annual change over 10+ years produces tens of thousands in outcome difference.
2. **Time horizon** — short stays (<5 years) are overwhelmed by transaction costs of 8–15% round-trip. The average homeowner stays **13 years**, well past most breakeven points.
3. **Mortgage rate** — at 3% vs. 7%, the monthly payment difference on a $400K home exceeds $800/month.
4. **Rent growth rate** — higher rent growth favors buying, as the fixed mortgage becomes relatively cheaper.
5. **Alternative investment returns** — determines the renter's wealth-building trajectory. A 6% vs. 10% assumption swings outcomes dramatically.

The **price-to-rent ratio** offers a quick screening tool: below 15 favors buying, 15–20 is neutral, above 21 favors renting. Currently, San Jose sits at **37–45** (strongly rent), while Cleveland is at **~11** (strongly buy). The national average hovers around **15–18**.

The forced savings argument has strong academic backing. Bernstein and Koudijs (QJE 2024) found that **$1 of mortgage amortization translates to approximately $1 of net wealth accumulation** — households do not reduce other savings to offset mortgage payments. Instead, they increase labor supply (~25%) and reduce consumption (~75%). This makes the mortgage a powerful commitment device, but one whose benefits concentrate in the later years of the loan when principal repayment dominates.

---

## The 2023–2025 landscape: a historically unusual market

The current high-rate environment has fundamentally altered the calculus. **Mortgage payments now exceed comparable rents by 52%** nationally (CBRE 2024) — a historically extreme premium. In **48 of 50 largest U.S. metros**, monthly mortgage payments exceed rent. Only Cleveland and Pittsburgh have cheaper mortgages than rent. Home prices hit **5× median household income** in early 2024, the highest affordability gap on record. The median income needed to buy the median home reached **$124,000** versus actual median income of $79,000.

Florida Atlantic University's Beracha-Hardin-Johnson Buy vs. Rent Index, which has demonstrated **statistically significant predictive power** for future price changes, showed most major metros in rent-favorable territory through 2023–2024. Cities where renting makes more financial sense include Atlanta, Denver, Dallas, LA, Miami, Seattle, and Portland. Cities in buy territory include Chicago, Cleveland, Cincinnati, Detroit, and surprisingly New York.

A critical emerging factor is **homeowner's insurance**, up **40.4% cumulatively from 2019–2024**. In disaster-prone states like Florida, Louisiana, and Oklahoma, annual premiums now exceed $5,000–6,000, adding 1–1.5% of home value in unrecoverable costs that most older models underweight. This alone can push Felix's 5% rule toward 6–7% in affected markets.

For anyone building a model today, the key adjustment is recognizing that the historical benchmarks (3.5% nominal appreciation, 3% rent growth, ~4% mortgage rates) that underpinned most classic analyses no longer apply. The model should accommodate **scenario analysis across rate environments**: at 3% rates, breakeven is ~3–4 years; at current 6.5%+, it stretches to **7–11 years**. The most robust approach is Monte Carlo simulation with correlated draws, but even a deterministic model with two-way sensitivity tables (appreciation vs. investment returns, holding period vs. mortgage rate) will capture the key dynamics.

## Conclusion

The best rent-vs-buy models share a common architecture: month-by-month simulation of buyer equity versus renter portfolio, with the Poterba user-cost framework providing the theoretical foundation and the NYT-style opportunity-cost comparison providing the practical implementation. Three insights stand out as genuinely important for model design. First, the "excess itemization" approach to tax benefits is essential — assuming full deductibility overstates homeownership's advantage by thousands per year post-TCJA. Second, Monte Carlo simulation reveals that deterministic models systematically understate the variance of outcomes due to Jensen's inequality and the nonlinear effects of leverage. Third, the forced-savings mechanism documented by Bernstein and Koudijs is not a behavioral footnote but a **first-order wealth driver** — any model that assumes renters perfectly invest all savings overstates the renter's likely outcome. The theoretically correct comparison (disciplined renter vs. buyer) often favors renting at today's rates, but the behaviorally realistic comparison (typical renter vs. typical buyer) still overwhelmingly favors buying for most households.
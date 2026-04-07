# The 2% you lose to volatility drag — and why it changes everything

**The "10% stock market return" everyone cites is the geometric mean (CAGR), not the arithmetic mean — and using the wrong one in a deterministic financial model overstates terminal wealth by roughly 50% over 30 years.** The S&P 500's long-run arithmetic average annual return is approximately **12%**, while its compound annual growth rate (geometric mean) is approximately **10%**. The ~2 percentage point gap is not a rounding error or a data discrepancy. It is a precise mathematical consequence of volatility: with an annual standard deviation of ~20%, the formula g ≈ a − σ²/2 predicts exactly the observed gap. This distinction matters enormously for anyone building a financial projection, running a rent-vs-buy calculation, or planning for retirement.

## S&P 500: the 12% you earn vs. the 10% you keep

Four authoritative datasets converge on remarkably consistent numbers for U.S. large-cap equity returns. Damodaran's NYU Stern database (1928–2024, 97 years) shows an arithmetic mean total return of **11.79%** and a geometric mean of approximately **9.8%**, with a standard deviation of **~19.5%**. The Ibbotson/Morningstar SBBI dataset (1926–present), long considered the gold standard, reports an arithmetic mean of **~12.0%**, a geometric mean of **~10.3%**, and a standard deviation of **~19.8%**. The Dimson-Marsh-Staunton database published in the UBS Global Investment Returns Yearbook (1900–2024, 125 years) shows a nominal geometric return of **9.7%** with real returns of **6.6%**. Shiller's dataset, extending back to 1871, yields a CAGR of roughly **10.4%** for the 1926–2026 period. All figures are nominal total returns including reinvested dividends.

The gap between arithmetic and geometric means across all these sources falls in a tight **1.8–2.1%** band. This is not coincidence. Given σ ≈ 20%, the half-variance formula predicts a gap of ½ × (0.20)² = **2.0%** — matching observed data almost exactly. Jacquier, Kane, and Marcus verified this in their 2003 *Financial Analysts Journal* paper using SBBI data: arithmetic mean of 12.49%, geometric mean of 10.51%, gap of 1.98%, predicted gap of 2.06%. The approximation works to within a few basis points.

| Source | Period | Arithmetic mean | Geometric mean (CAGR) | Std. deviation | Gap | ½σ² |
|---|---|---|---|---|---|---|
| **Ibbotson/SBBI** | 1926–present | ~12.0% | ~10.3% | ~19.8% | ~1.7% | ~2.0% |
| **Damodaran (NYU)** | 1928–2024 | ~11.8% | ~9.8% | ~19.5% | ~2.0% | ~1.9% |
| **DMS/UBS Yearbook** | 1900–2024 | ~11.7% (est.) | 9.7% | ~20% | ~2.0% | ~2.0% |
| **Shiller** | 1871–2026 | — | ~10.4% | ~20% | — | — |

All figures are nominal total returns including dividends.

## The volatility drag formula is exact under lognormality

The relationship g ≈ a − σ²/2 is not merely an approximation — it is exact when returns follow geometric Brownian motion (the standard assumption underlying Black-Scholes and most of quantitative finance). In continuous-time notation, if the arithmetic drift is μ and volatility is σ, the continuously compounded growth rate is μ − σ²/2. Tom Messmore formalized this as "variance drain" in a 1995 *Journal of Portfolio Management* paper. The intuition is pure Jensen's inequality: because the logarithm is concave, the expected log of a random variable is less than the log of its expectation. Variable returns always compound to less than the average return compounded — unless volatility is zero.

The practical impact scales quadratically with volatility. For bonds (σ ≈ 3%), the drag is negligible at **0.05%**. For the S&P 500 (σ ≈ 20%), it is a material **2.0%**. For small-cap stocks (σ ≈ 32%), it balloons to roughly **5.1%** — explaining why small caps' arithmetic premium over large caps substantially shrinks on a CAGR basis. Kitces demonstrated empirically that for a 2007–2016 S&P 500 sample (arithmetic mean 8.75%, σ = 18.86%), the formula predicted a geometric mean of 6.97% versus the actual 6.94%, an error of just 3 basis points.

A concrete example crystallizes the stakes. Compounding $100,000 at 12% (arithmetic mean) for 30 years yields **$2,996,000**. Compounding at 10% (geometric mean) yields **$1,745,000**. That is a **72% overstatement** from using the wrong mean — not a theoretical quibble, but the difference between a comfortable and a catastrophic retirement plan.

## U.S. housing: low returns, but even lower volatility changes the math

Housing data requires careful disaggregation of price appreciation from total returns (including imputed rent). The landmark Jordà-Schularick-Taylor dataset ("The Rate of Return on Everything," *QJE* 2019), covering 1891–2015 across 16 countries, provides the most comprehensive picture. For the United States, nominal price-only appreciation averaged **3.54%** arithmetically with a standard deviation of just **8.24%**. Adding imputed rental income of ~5.3%, total nominal housing returns averaged **8.87%** arithmetically with σ of **8.40%**. Shiller's famous finding — that real (inflation-adjusted) housing appreciation is roughly **0.5–1.0%** annually from 1890 to present — remains intact, though a 2024 Philadelphia Fed working paper suggests his pre-1975 data may modestly understate true appreciation.

| Metric | U.S. Housing (Jordà) | U.S. Equities (Jordà) |
|---|---|---|
| Nominal arithmetic total return | **8.87%** | **11.08%** |
| Nominal total return σ | **8.40%** | **18.45%** |
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

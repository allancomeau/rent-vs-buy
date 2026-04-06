# Defaults Rationale: Every Number, Sourced and Explained

Every default value in the Rent vs. Buy simulation traces to a specific data source. This document explains what each parameter is, why the default was chosen, what range is reasonable, and how sensitive the model is to changes. The sensitivity rankings come from our research synthesis (Himmelberg-Mayer-Sinai, Poterba, Ben Felix).

Parameters are grouped by how they enter the model. "Sensitivity" is rated Low/Medium/High/Critical based on how much a ±1% change in the parameter shifts the 15-year outcome.

---

## Your Situation (Personal Variables)

### Monthly Payment
- **Default:** $2,000/mo (US)
- **What it is:** Your current monthly housing outflow — rent if you're renting, or the mortgage P&I payment you're considering. This is the model's anchor: both the buyer and renter start from this same cash outflow.
- **Source:** Approximates the national median. NAR Q4 2025 median home price of $414,900 at 6.0%/30yr with 20% down produces a $1,991/mo P&I payment. Zillow national average rent is ~$2,000/mo (Mar 2026).
- **Reasonable range:** $1,000–$5,000 for most US households; $500–$8,000 globally.
- **Sensitivity:** Critical — this determines the derived home price and therefore the scale of every other calculation.
- **Warning triggers:** Below $800 or above $6,000 (US): "This is outside the typical range for US households."

### Down Payment
- **Default:** $80,000 (US)
- **What it is:** Cash the buyer puts toward the home purchase. The renter invests this amount instead (plus closing costs the buyer would have paid).
- **Source:** 20% of the ~$400K derived home price. NAR 2024 Profile of Home Buyers shows median down payment of 15% for all buyers, 8% for first-time buyers. 20% avoids PMI.
- **Reasonable range:** $10,000–$300,000 for most scenarios.
- **Sensitivity:** High — directly determines the renter's starting portfolio, which compounds over the full simulation. A $20K difference compounds to ~$100K+ over 15 years at 10%.
- **Warning triggers:** Implied down payment percentage below 5%: "PMI would apply (not modeled). Results may understate buyer costs." Above 50%: "Unusual — most buyers finance a majority of the purchase."

### Mortgage Term
- **Default:** 30 years (US), 25 years (CA/UK), 20 years (DE)
- **What it is:** Length of the mortgage. Longer terms mean lower monthly payments but more interest paid.
- **Source:** US: 30-year fixed is the dominant product (~90% of originations, Freddie Mac). Canada: 25-year amortization is standard (max for insured mortgages). UK: 25 years typical. Germany: 20-year Annuitätendarlehen common.
- **Reasonable range:** 15–30 years.
- **Sensitivity:** Medium — affects amortization schedule and when the buyer's costs drop (post-payoff). Shorter terms mean higher payments but faster equity buildup.
- **Warning triggers:** Below 10 or above 40: "Unusual mortgage term."

### Sell House in Year (Holding Period)
- **Default:** 15 years
- **What it is:** When the buyer sells and the model evaluates the outcome. This is the "decision point."
- **Source:** NAR 2024: median homeowner tenure is 10 years, up from 6 years pre-2008. 15 years is a reasonable planning horizon for someone in their mid-to-late twenties buying a home they might stay in through their thirties and into their forties.
- **Reasonable range:** 3–40 years. Below 5 years, transaction costs (~9% round-trip) overwhelm almost any appreciation.
- **Sensitivity:** Critical — the single most important user choice. Short holds almost always favor renting. Long holds almost always favor buying. The crossover is typically 5–10 years.
- **Warning triggers:** Below 3: "Transaction costs alone (~9% of home value) make buying very unlikely to win at this horizon." Above 40: "Consider that life circumstances, not just finances, typically drive the decision to sell."

### Renter Discipline
- **Default:** 100% (theoretical)
- **What it is:** What percentage of the renter's monthly savings (the difference when renting is cheaper than buying) actually gets invested. At 100%, the renter perfectly invests every dollar of surplus. At 60%, they invest 60% and spend 40%.
- **Source:** Bernstein & Koudijs, "The Mortgage Piggy Bank" (QJE 2024). Key finding: $1 of mortgage amortization translates to ~$1 of net wealth accumulation — homeowners don't reduce other savings to offset mortgage payments. Instead, they increase labor supply (~25%) and reduce consumption (~75%). This means mortgages function as a powerful forced-savings device. Renters, lacking this forced mechanism, rarely invest all surplus. The Federal Reserve SCF 2019 showed median renter net worth of $6,300 vs. $255,000 for homeowners — partly due to income differences, but discipline is a major factor.
- **Reasonable range:** 50–100%. Academic literature suggests 60–75% is behaviorally realistic for the typical renter. 100% is the theoretical maximum that gives renting its best case.
- **Sensitivity:** High — at 60% discipline, the renter's portfolio at year 15 can be 30–40% smaller than at 100%. This is often the variable that flips the verdict from "rent" to "buy."
- **Recommended for family discussion:** Run at 100% first (theoretical best case for renting), then at 70% (realistic). If the verdict flips, discipline is the deciding factor — not the market.
- **Warning triggers:** Below 40%: "At this level, the renter is spending most of their savings advantage. Consider whether this reflects your actual behavior." Above 100%: not possible (capped).

---

## Growth Rates

### Investment Return
- **Default:** 10.0% nominal (US)
- **What it is:** The annual return earned on invested cash — both the renter's portfolio (seeded with down payment + closing costs) and the buyer's portfolio (surplus invested after costs drop, typically post-mortgage-payoff).
- **Source:** S&P 500 long-run geometric mean (CAGR), the correct metric for deterministic compounding:
  - Ibbotson/SBBI (1926–present): ~10.3% geometric, ~12.0% arithmetic
  - Damodaran/NYU (1928–2024): ~9.8% geometric, ~11.8% arithmetic
  - Dimson-Marsh-Staunton (1900–2024): ~9.7% geometric
  - The ~2% gap between arithmetic and geometric is **volatility drag**: g ≈ a − σ²/2 = 12% − ½(0.20)² = 10%
- **Why geometric, not arithmetic:** Our model compounds deterministically (smooth line, no year-to-year variation). The geometric mean is the rate at which money actually grows when compounded. Using the arithmetic mean (12%) in a deterministic model overstates terminal wealth by ~50–70% over 30 years. If we were running Monte Carlo (random yearly draws), we would use the arithmetic mean, because the simulation's randomness naturally creates the drag. We're not, so we use geometric.
- **Reasonable range:** 6–12% nominal. Below 6% implies persistently poor equity returns. Above 12% exceeds the long-run arithmetic mean and is not sustainable.
- **Sensitivity:** Critical — this is one of the two most important variables (along with mortgage rate). The spread between investment return and mortgage rate largely determines whether renting or buying wins.
- **Warning triggers:** Above 12%: "This exceeds the S&P 500's long-run arithmetic mean (~12%). The model uses geometric returns for deterministic compounding — 10% is the correct long-run benchmark." Below 5%: "This is below long-run bond returns and implies a very conservative investment strategy."

### Home Appreciation
- **Default:** 4.2% nominal (US national 5yr CAGR); varies by metro
- **What it is:** Annual rate at which the home's value increases.
- **Source:** FHFA HPI 5-year purchase-only CAGR through Q4 2025 for metro-level data. Long-run benchmarks:
  - Shiller (1890–present): ~0.5–1.0% real ≈ 3.5–4.0% nominal
  - FHFA (1991–2026): ~4.3% nominal CAGR
  - Jordà-Schularick-Taylor (1891–2015): 3.54% nominal arithmetic, ~3.2% geometric for US price-only
  - NAR 50-year: ~5.5% nominal (inflated by quality improvements in housing stock)
- **Why this matters more than you think:** Leverage amplifies appreciation. With 20% down, a 4% home appreciation on a $400K home is $16K/year — a 20% return on the $80K down payment. This leverage effect is why appreciation is the #1 sensitivity variable in most academic models.
- **Volatility drag note:** Housing σ ≈ 6–8%, so drag is only ~0.2–0.3% (vs. ~2% for equities). This means the arithmetic vs. geometric distinction matters much less for housing.
- **Reasonable range:** 1–7% nominal. Below 1% implies real depreciation. Above 7% has not been sustained by any major US metro over 10+ years.
- **Sensitivity:** Critical — amplified by leverage. A 1% change in appreciation shifts the 15-year buyer wealth by $40K–$80K depending on home price.
- **Warning triggers:** Above 8%: "No major US metro has sustained this rate over a full market cycle. Recent 5-year CAGRs include the post-pandemic surge and may not be representative." Below 0%: "This implies the home loses value every year. While possible in specific markets (e.g., post-2008), it's unusual to plan for."

### Rent Growth
- **Default:** 3.3% (US national)
- **What it is:** Annual rate at which rent increases. Since starting rent equals the mortgage payment, this determines when renter costs exceed buyer costs.
- **Source:** Long-run US average ~3.0–3.5% nominal. Gallin (Fed 2004) showed house prices and rents are cointegrated long-run — they track each other over decades but can diverge for years.
- **Reasonable range:** 1–6%. Below 2% is historically unusual except in overbuilt markets. Above 5% is post-pandemic-level growth (Miami 7.4% CAGR 2020–2026) and unlikely to persist.
- **Sensitivity:** Medium-High — determines the "cost crossover" year when rising rent makes the buyer's fixed mortgage look cheap.
- **Warning triggers:** Above 6%: "This rate of rent growth has only been seen in a few post-pandemic hotspots and is unlikely to persist." Below 1%: "Historically unusual — even in flat markets, rents tend to track inflation."

### Inflation
- **Default:** 2.5%
- **What it is:** General price inflation. Drives insurance cost growth in the model.
- **Source:** Fed 2% target. Current 2024–2025 CPI running ~2.5–3.0%. Long-run US average (1926–present) ~3%.
- **Reasonable range:** 1.5–4.0%.
- **Sensitivity:** Low — only affects insurance cost escalation. Home appreciation and rent growth are modeled independently.
- **Warning triggers:** Above 5% or below 0%: "Unusual inflation assumption."

---

## Ownership Costs

### Mortgage Rate
- **Default:** 6.0% (US, Feb 2026 30yr fixed average per Redfin)
- **What it is:** The interest rate on the mortgage. Determines monthly payment and, in our model, the derived home price (since monthly payment is the input).
- **Source:** Freddie Mac PMMS / Redfin. Feb 2026 national average 30yr fixed: 6.0%. Range over past 3 years: 6.0–7.8%. Long-run average (1971–present): ~7.7%. Pre-2022 decade average: ~3.5–4.5%.
- **Reasonable range:** 3–9%. Below 3% has only occurred briefly (2020–2021). Above 9% hasn't been seen since the early 1990s.
- **Sensitivity:** Critical — the most impactful cost variable (Himmelberg, Mayer, Sinai 2005). A 1% rate difference on a $320K loan changes monthly payment by ~$200 and total interest paid by ~$70K over 30 years. In our model, changing the rate also changes the derived home price (same payment buys a different home).
- **Warning triggers:** Above 9%: "This rate hasn't been seen since the early 1990s." Below 2.5%: "This rate was briefly available in 2020–2021 but is unlikely in the current environment."

### Property Tax Rate
- **Default:** 0.89% (US national effective rate)
- **What it is:** Annual property tax as a percentage of home value. Paid regardless of mortgage status.
- **Source:** NAHB analysis of Census ACS 2024: national effective rate $8.88 per $1,000 of home value = 0.888%. Varies enormously by state: Hawaii 0.29%, New Jersey 2.23%, Illinois 1.88%. Metro-level data from LendingTree/Census ACS: Buffalo 2.11%, Phoenix 0.48%, Chicago 2.08%.
- **Reasonable range:** 0.2–2.5%.
- **Sensitivity:** Medium — property tax is an unrecoverable cost that directly reduces buyer wealth. A 1% rate on a $400K home = $4,000/year. This is also subject to the SALT deduction cap ($40,400 for 2026).
- **Warning triggers:** Above 2.5%: "This would be among the highest effective rates in the US." Below 0.2%: "Only Hawaii and a few special jurisdictions are this low."

### Maintenance
- **Default:** 1.0% of home value per year
- **What it is:** Cost of maintaining the property — repairs, upkeep, replacements. Grows with home value (as the home appreciates, the replacement cost of its components increases).
- **Source:** Industry standard "1% rule," which traces to Poterba's depreciation rate (δ) in his user cost formula (QJE 1984). Empirical support: National Association of Home Builders estimates $3,000–$6,000/year for a median-priced home. On a $400K home, 1% = $4,000/year.
- **Reasonable range:** 0.5–2.0%. Newer homes trend lower; older homes or coastal properties trend higher.
- **Sensitivity:** Low-Medium — contributes to unrecoverable costs but is relatively stable.
- **Warning triggers:** Above 2.5%: "Unusually high — even for older homes." Below 0.3%: "Unrealistically low. All homes require ongoing maintenance."

### Homeowner's Insurance
- **Default:** 0.63% of original home value (US national avg)
- **What it is:** Annual insurance premium, expressed as a percentage of the original purchase price. Grows with inflation (not home value — policies don't automatically adjust coverage).
- **Source:** NerdWallet/Bankrate 2026: national average ~$2,490/year for $400K dwelling coverage = 0.62%. Varies dramatically: Hawaii $659/yr (0.16%), Florida $7,136/yr (1.78%), Nebraska $6,400+/yr. Post-2019 cumulative increase of 40%+ nationally.
- **Reasonable range:** 0.2–2.0%. Florida, Louisiana, Oklahoma, Nebraska are extreme outliers above 1.5%.
- **Sensitivity:** Low-Medium nationally; High in disaster-prone states. In Florida, insurance can add 1.5–2% of home value to unrecoverable costs — enough to push Ben Felix's "5% rule" to "7–8%."
- **Warning triggers:** Above 2%: "This is in the range of Florida/Louisiana disaster-area premiums. Verify with actual quotes." Below 0.1%: "Unrealistically low — even low-risk states average ~0.3%."

### Renter's Insurance
- **Default:** $300/year (US)
- **What it is:** Annual renter's insurance premium. Much cheaper than homeowner's because it only covers personal property, not the structure.
- **Source:** NerdWallet 2024: national average $148–$180/year for basic coverage; $300–$400 for comprehensive. We default to $300 as a reasonable mid-point.
- **Reasonable range:** $100–$600.
- **Sensitivity:** Negligible — this is the least impactful variable in the entire model.

---

## Transaction Costs

### Buying Closing Costs
- **Default:** 3% of home price
- **What it is:** Fees paid at purchase — origination, appraisal, title insurance, recording fees, etc.
- **Source:** Bankrate 2024: average closing costs (excluding taxes) = 2–5% of purchase price. 3% is a common planning assumption.
- **Note:** These costs are part of the buyer's total upfront outflow. The renter's portfolio is seeded with down payment + closing costs — the buyer's entire cash commitment that the renter didn't have to make.
- **Reasonable range:** 2–5%.
- **Sensitivity:** Low — one-time cost.

### Selling Costs
- **Default:** 6% of sale price
- **What it is:** Costs incurred when the buyer sells — agent commissions (typically 5–6%), transfer taxes, staging, etc.
- **Source:** NAR: typical total agent commission 5–6% (post-Sitzer/Burnett settlement, buyer agent commissions are negotiable but still common). Total selling costs including transfer taxes and prep: 6–8%.
- **Reasonable range:** 3–8%. FSBO or discount brokerages can reduce this to 3–4%.
- **Sensitivity:** Medium — applied to the full home value at sale. On a $500K home, 6% = $30K subtracted from buyer wealth.
- **Warning triggers:** Above 8%: "Unusually high for US markets." Below 2%: "Only achievable with FSBO and no buyer agent compensation."

---

## Tax Parameters

### Marginal Tax Rate
- **Default:** 22% (US); 0% (all other countries)
- **What it is:** The tax rate applied to the incremental tax benefit from mortgage interest and property tax deductions. Only applies in the US — mortgage interest on primary residences is not deductible in Canada, UK, Australia, or Germany.
- **Source:** US: 22% bracket covers $44,726–$95,375 for single filers (2025). This is the most common bracket for the target audience (mid-to-late twenties professionals). 24% bracket starts at $95,376. For MFJ: 22% covers $89,451–$190,750.
- **Note:** The model uses "excess itemization" — the tax benefit is max(0, Itemized − Standard Deduction) × Rate. At the 22% bracket with a $30K standard deduction, many homeowners get zero incremental tax benefit because their itemized deductions don't exceed the standard deduction.
- **Reasonable range (US):** 12–37%. The model is most sensitive to this in high-tax states where itemized deductions exceed the standard deduction.

### Standard Deduction
- **Default:** $30,000 (US, MFJ 2025 per OBBBA)
- **What it is:** The baseline deduction all taxpayers get. The mortgage interest deduction only produces a benefit to the extent that total itemized deductions EXCEED this amount.
- **Source:** IRS 2025: $15,000 single, ~$30,000 MFJ (OBBBA increased by ~$1,500).
- **Key insight:** Post-TCJA, the large standard deduction means most homeowners — especially those with smaller mortgages or in low-tax states — get zero incremental tax benefit from their mortgage. This was a major change from pre-2018 when the standard deduction was ~$12,000.

### SALT Cap
- **Default:** $40,400 (US, 2026 per OBBBA)
- **What it is:** Maximum deductible amount for state and local taxes (property tax + state income tax combined).
- **Source:** One Big Beautiful Bill Act (signed July 2025): $40,000 for 2025, $40,400 for 2026, +1% annually through 2029, reverts to $10,000 in 2030. Phases down for MAGI above $500,000.
- **Previous value:** $10,000 (TCJA 2018–2024). The quadrupling of this cap significantly increases the tax benefit for homeowners in high-tax states (NY, NJ, CT, CA, IL) who itemize.

### Capital Gains Rate
- **Default:** 15% (US)
- **What it is:** Tax rate on investment gains (portfolio) and home sale gains exceeding the exclusion.
- **Source:** IRS 2025: 0% for income below $44,625 (single), 15% for $44,626–$492,300, 20% above. 15% covers the vast majority of the target audience.
- **Reasonable range:** 0–20%.

### Capital Gains Exclusion (Home Sale)
- **Default:** $500,000 (US, MFJ §121)
- **What it is:** Amount of home sale profit exempt from capital gains tax. Must have owned and lived in the home for 2 of the last 5 years.
- **Source:** IRC §121. $250,000 single, $500,000 MFJ. Has not been adjusted for inflation since 1997.
- **International:** Canada, UK, Australia — full primary residence exemption (modeled as $999,999,999). Germany — exempt after 10-year holding period (modeled as 0% rate).
- **Sensitivity:** Low for most scenarios — few homes appreciate by >$500K within a typical holding period.

---

## Simulation Parameters

### Simulation Length
- **Default:** 30 years
- **What it is:** How many years the model simulates. Doesn't affect the "verdict" (which uses holding period), but shows long-term trajectories.
- **Reasonable range:** 10–65. Beyond 40 years, assumptions about constant rates become increasingly unreliable.

---

## Volatility Reference (Not Yet in Model — For Default Validation)

These standard deviations are included for reference. They power the volatility drag calculation and will be used for input validation warnings.

| Asset | Arithmetic Mean | σ (Std Dev) | Volatility Drag (½σ²) | Geometric Mean |
|---|---|---|---|---|
| S&P 500 | ~12.0% | ~20% | ~2.0% | ~10.0% |
| US Housing (price-only) | ~3.5% | ~8% | ~0.3% | ~3.2% |
| US Bonds | ~5.5% | ~3% | ~0.05% | ~5.45% |

Sources: Ibbotson/SBBI, Damodaran, Jordà-Schularick-Taylor, Kitces.

**The key asymmetry for rent vs. buy:** Equities lose ~2% to volatility drag; housing loses ~0.3%. This means the "10% stocks vs. 4% housing" comparison that dominates popular advice overstates the renter's advantage relative to a world with realistic volatility. The true compounded gap is closer to 10% vs. 3.7% — still significant, but narrower than the arithmetic comparison (12% vs. 3.5%) would suggest.

---

## Parameter Interaction Notes

Some parameters interact in ways that aren't obvious:

1. **Investment Return vs. Mortgage Rate (the "spread"):** This is the dominant first-order variable. When investReturn > mortgageRate, the renter's portfolio grows faster than the buyer's equity (in percentage terms). When the spread is negative (rare historically), buying is almost always better.

2. **Appreciation + Leverage:** A 4% appreciation on a home with 20% down is effectively a 20% return on the equity invested. This is why appreciation is so powerful — but it works both ways. A -4% year (which happens) wipes out the entire down payment.

3. **Rent Growth vs. Fixed Mortgage:** Over time, the mortgage payment stays fixed while rent grows. Even if renting starts cheaper, the cost crossover is inevitable if rent grows faster than 0%. After the crossover, the buyer invests the surplus — this is the "symmetric surplus investing" feature.

4. **Discipline × Holding Period:** These interact nonlinearly. Low discipline + short hold = renting almost never wins. High discipline + long hold = renting often wins early but buying catches up after mortgage payoff. The inflection depends on all other variables.

5. **SALT Cap × Marginal Tax Rate × Home Price:** The tax benefit is max(0, Interest + min(PropTax, SALT Cap) + Other − StdDeduction) × MarginalRate. With the new $40,400 SALT cap, more homeowners will itemize, increasing the tax benefit. But for homes under ~$300K in low-tax states, the standard deduction still exceeds itemized deductions, and the tax benefit is $0.

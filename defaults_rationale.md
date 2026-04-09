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

### Investment Discipline
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

## Volatility Reference (Used for Default Validation & Callout Display)

These standard deviations are included for reference. They power the volatility drag calculation and will be used for input validation warnings.

| Asset | Arithmetic Mean | σ (Std Dev) | Volatility Drag (½σ²) | Geometric Mean |
|---|---|---|---|---|
| S&P 500 | ~12.0% | ~20% | ~2.0% | ~10.0% |
| US Housing (price-only) | ~3.5% | ~8% | ~0.3% | ~3.2% |
| US Bonds | ~5.5% | ~3% | ~0.05% | ~5.45% |

Sources: Ibbotson/SBBI, Damodaran, Jordà-Schularick-Taylor, Kitces.

**The key asymmetry for rent vs. buy:** Equities lose ~2% to volatility drag; housing loses ~0.3%. This means the "10% stocks vs. 4% housing" comparison that dominates popular advice overstates the renter's advantage relative to a world with realistic volatility. The true compounded gap is closer to 10% vs. 3.7% — still significant, but narrower than the arithmetic comparison (12% vs. 3.5%) would suggest.

---

## International Defaults — 16 Countries

The model supports 16 countries and 54 metros. International differences are expressed entirely through parameter values — the simulation engine has zero conditional logic for country-specific rules. This section documents every country's defaults and why they were chosen.

### Tax Treatment Buckets

The most important international distinction is whether mortgage interest on a primary residence is tax-deductible. Three buckets:

**Bucket A — No deduction (marginalTax = 0):** Canada, UK, Australia, Germany, Ireland, New Zealand, France, Spain, Italy, Japan, Singapore, South Korea. In these countries, the buyer gets $0 tax benefit from mortgage interest. This is the global norm — the US is the outlier.

**Bucket B — Partial deduction (marginalTax > 0, with disclaimer):** Netherlands (hypotheekrenteaftrek at 37% base rate, being phased down), Sweden (30% of interest paid). These genuinely have mortgage interest deductions and use the engine's tax module correctly. Switzerland has an imputed-rental-income-offset system too complex to model cleanly — set to marginalTax = 0 as a conservative simplification.

**US — Full deduction:** The only country with a broad mortgage interest deduction. Modeled with excess itemization method (post-TCJA correct).

### Country-by-Country Reference

Each entry lists: personal defaults (monthly payment, down payment), structural defaults (mortgage rate, term, tax treatment), national market data (appreciation, rent growth, property tax, insurance), and key notes.

---

### 🇺🇸 United States
- **Personal defaults:** $2,000/mo, $80,000 down
- **Mortgage rate:** 6.0% (30yr fixed, Freddie Mac PMMS / Redfin Feb 2026)
- **Mortgage term:** 30 years (dominant product, ~90% of originations)
- **Tax bucket:** Full deduction. marginalTax 22%, stdDeduction $30,000 (MFJ, OBBBA 2025), SALT cap $40,400 (OBBBA 2026)
- **Capital gains:** 15% rate, $500,000 MFJ exclusion (§121)
- **Investment return:** 10.0% (S&P 500 geometric mean)
- **National market:** Appreciation 4.2% (5yr CAGR, FHFA HPI), rent growth 3.3%, property tax 0.89% (NAHB/Census ACS 2024), insurance 0.63% (NerdWallet 2026)
- **Closing/selling:** 3% / 6%
- **Sources:** FHFA, Zillow ZHVI/ZORI, NAR, Census ACS, Freddie Mac, NerdWallet, Bankrate

### 🇨🇦 Canada
- **Personal defaults:** C$2,500/mo, C$100,000 down
- **Mortgage rate:** 5.0% (5yr fixed, Bank of Canada benchmark rate, early 2026)
- **Mortgage term:** 25 years (max for insured mortgages; 5yr renewable terms standard)
- **Tax bucket:** A (no deduction). Mortgage interest on primary residence is NOT deductible in Canada.
- **Capital gains:** 16.7% effective rate on investment gains. Primary residence fully exempt — modeled as capGainsExclusion = 999999999.
- **Investment return:** 8.0% (TSX + global diversified, lower than US due to smaller domestic market)
- **National market:** Appreciation 3.0% (5yr), rent growth 4.0%, property tax 0.80%, insurance 0.40%
- **Closing/selling:** 3% / 5%
- **Key notes:** 5yr renewable term means rate resets are a structural risk not captured by the model. CMHC mortgage insurance required below 20% down — not modeled. Smith Manoeuvre (converting to deductible debt via investment loan) is a legal strategy but too complex to model.
- **Sources:** CREA, Teranet HPI, CMHC, Bank of Canada, Ratehub

### 🇬🇧 United Kingdom
- **Personal defaults:** £1,500/mo, £60,000 down
- **Mortgage rate:** 4.5% (5yr fixed, Bank of England data, early 2026)
- **Mortgage term:** 25 years (standard; 30-35yr becoming more common)
- **Tax bucket:** A (no deduction). MIRAS (mortgage interest relief) abolished 2000.
- **Capital gains:** 18% rate (higher rate taxpayers). Primary residence fully exempt (Private Residence Relief) — capGainsExclusion = 999999999.
- **Investment return:** 8.0%
- **National market:** Appreciation 2.0% (5yr), rent growth 4.0%, property tax 0.40% (Council Tax varies enormously by band/council — this is a rough national average), insurance 0.30%
- **Closing/selling:** 4% / 3%
- **Key notes:** Stamp Duty Land Tax (SDLT) is a major upfront cost captured in closingPct. Council Tax is technically a banded tax on property value, not a percentage — the 0.40% is an approximation. Leasehold vs freehold distinction not modeled.
- **Sources:** ONS UK HPI, Rightmove, Halifax, UBS RE Bubble Index 2025, BoE

### 🇦🇺 Australia
- **Personal defaults:** A$2,500/mo, A$100,000 down
- **Mortgage rate:** 6.0% (variable, RBA cash rate + spread, early 2026)
- **Mortgage term:** 30 years
- **Tax bucket:** A (no deduction on owner-occupied). Negative gearing applies only to investment properties — not modeled.
- **Capital gains:** 23.5% effective rate. Primary residence fully exempt — capGainsExclusion = 999999999.
- **Investment return:** 8.0% (ASX + global)
- **National market:** Appreciation 5.0% (5yr), rent growth 4.5%, property tax 0.30%, insurance 0.40%
- **Closing/selling:** 4% / 3%
- **Key notes:** Stamp duty varies dramatically by state (NSW vs VIC vs QLD). First Home Owner Grant not modeled. Variable rate is dominant — fixed rates less common than in US/UK.
- **Sources:** CoreLogic, SQM Research, QBE, ABS, APRA, RBA

### 🇩🇪 Germany
- **Personal defaults:** €1,500/mo, €60,000 down
- **Mortgage rate:** 3.5% (10yr fixed, Bundesbank data, early 2026; ECB easing cycle)
- **Mortgage term:** 20 years (Annuitätendarlehen; 10-15yr fixed periods common, then rate resets)
- **Tax bucket:** A (no deduction on owner-occupied)
- **Capital gains:** 26% rate (Abgeltungsteuer) on investments. Primary residence exempt after 10-year holding period — modeled as capGainsExclusion = 999999999 (assumes >10yr hold, which is the target audience).
- **Investment return:** 7.0% (more conservative domestic market)
- **National market:** Appreciation 1.5% (5yr), rent growth 3.0%, property tax 0.30%, insurance 0.20%
- **Closing/selling:** 7% / 3%
- **Key notes:** Grunderwerbsteuer (transfer tax) is 3.5–6.5% by state — captured in the high 7% closingPct. Notary and land registry fees add ~1.5%. German closing costs are among the highest in Europe. Mietpreisbremse (rent brake) limits rent increases in some cities — not modeled.
- **Sources:** Destatis, Bundesbank, ImmoScout24, UBS RE Bubble Index 2025

### 🇮🇪 Ireland
- **Personal defaults:** €1,800/mo, €60,000 down
- **Mortgage rate:** 3.9% (ECB-linked, Irish lenders avg, early 2026)
- **Mortgage term:** 30 years
- **Tax bucket:** A (no deduction). Mortgage interest relief abolished 2021.
- **Capital gains:** 33% rate. Primary residence fully exempt.
- **Investment return:** 8.0%
- **National market:** Appreciation 6.0% (5yr, CSO RPPI), rent growth 4.0%, property tax 0.10% (LPT is very low — banded system at ~0.09%), insurance 0.30%
- **Closing/selling:** 4% / 2.5%
- **Key notes:** LPT (Local Property Tax) is among the lowest property taxes in the developed world. Housing supply crisis driving persistent price and rent growth. Stamp duty 1% up to €1M, 2% above.
- **Sources:** CSO RPPI Aug 2025, Daft.ie, Revenue.ie LPT, KTA Tax

### 🇳🇿 New Zealand
- **Personal defaults:** NZ$2,800/mo, NZ$150,000 down
- **Mortgage rate:** 5.5% (2yr fixed, RBNZ B20, end-2025)
- **Mortgage term:** 30 years
- **Tax bucket:** A (no deduction on owner-occupied)
- **Capital gains:** 0% on primary residence. Bright-line test (investment properties only) not relevant for owner-occupiers.
- **Investment return:** 8.0%
- **National market:** Appreciation 0.5% (5yr, REINZ HPI — Auckland negative, rest of NZ positive), rent growth 3.5%, property tax 0.40% (council rates), insurance 0.40%
- **Closing/selling:** 3% / 3%
- **Key notes:** Auckland has been negative for 5 years (-0.9% CAGR). The national figure masks extreme regional variation. KiwiSaver HomeStart grant not modeled. DTI (debt-to-income) limits introduced — affect affordability but not modeled.
- **Sources:** REINZ HPI Dec 2025, Cotality NZ, RBNZ B20, MoneyHub NZ, Opes Partners

### 🇫🇷 France
- **Personal defaults:** €1,500/mo, €60,000 down
- **Mortgage rate:** 3.3% (20yr fixed, Banque de France, late 2025)
- **Mortgage term:** 20 years (standard in France; 25yr max under HCSF rules)
- **Tax bucket:** A (no deduction)
- **Capital gains:** 36% combined rate (19% CGT + 17.2% social charges). Primary residence fully exempt.
- **Investment return:** 7.0%
- **National market:** Appreciation 1.0% (5yr — includes 2023-24 correction), rent growth 2.5%, property tax 0.70% (taxe foncière, varies by commune), insurance 0.20%
- **Closing/selling:** 8% / 5%
- **Key notes:** Notaire fees (7-9% on existing properties) make French closing costs the highest in the model at 8%. This is a major structural headwind for short-term buyers. The HCSF 35% debt-to-income cap limits borrowing capacity — not modeled.
- **Sources:** INSEE RPPI, Notaires de France, Investropa, Banque de France

### 🇪🇸 Spain
- **Personal defaults:** €1,200/mo, €50,000 down
- **Mortgage rate:** 3.0% (Euribor-linked variable, ECB data, mid-2025)
- **Mortgage term:** 25 years
- **Tax bucket:** A (no deduction — abolished 2013)
- **Capital gains:** 23% rate. Primary residence exempt if proceeds reinvested in new primary residence within 2 years.
- **Investment return:** 7.0%
- **National market:** Appreciation 5.0% (5yr), rent growth 4.0%, property tax 0.50% (IBI, varies by municipality), insurance 0.20%
- **Closing/selling:** 10% / 4%
- **Key notes:** ITP (transfer tax) is 6-10% by region — captured in the high 10% closingPct. Variable-rate mortgages linked to Euribor are dominant, creating rate-reset risk. Strong post-pandemic rental growth driven by tourism demand.
- **Sources:** INE HPI, Idealista, UBS RE Bubble Index 2025, ECB

### 🇳🇱 Netherlands
- **Personal defaults:** €2,000/mo, €80,000 down
- **Mortgage rate:** 3.7% (10-20yr fixed, DNB/ECB, early 2026)
- **Mortgage term:** 30 years
- **Tax bucket:** B (partial deduction). ⚠️ Hypotheekrenteaftrek: mortgage interest IS deductible at the base tax rate (~37%), but this is being phased down. The model applies the deduction. The Netherlands also taxes imputed rental income (eigenwoningforfait, ~0.35% of home value) — NOT modeled, which slightly overstates buyer advantage.
- **Capital gains:** 0% on primary residence.
- **Investment return:** 8.0%
- **National market:** Appreciation 6.0% (5yr), rent growth 4.0%, property tax 0.20%, insurance 0.20%
- **Closing/selling:** 6% / 3%
- **Key notes:** The Dutch mortgage market is unique: interest-only mortgages were historically common (now restricted), and the tax deduction is a major policy lever. The eigenwoningforfait (imputed rent tax) partially offsets the deduction benefit — net effect is complex and canton/income-dependent.
- **Sources:** CBS Statline, ABN AMRO, Global Property Guide 2025, DNB

### 🇮🇹 Italy
- **Personal defaults:** €1,200/mo, €50,000 down
- **Mortgage rate:** 3.2% (ECB-linked, Banca d'Italia, mid-2025)
- **Mortgage term:** 25 years
- **Tax bucket:** A (technically a tiny deduction exists — 19% of interest up to €4,000 = max ~€760/yr — but too small to materially affect outcomes, not modeled)
- **Capital gains:** 26% rate. Primary residence exempt after 5 years.
- **Investment return:** 7.0%
- **National market:** Appreciation 1.0% (5yr), rent growth 2.5%, property tax 0.10% (IMU waived on primary residence — "prima casa"), insurance 0.20%
- **Closing/selling:** 8% / 4%
- **Key notes:** IMU (municipal property tax) is NOT charged on primary residences — only on second homes and investment properties. This is a significant buyer advantage not common in other countries. The low appreciation reflects a decade of near-flat prices; Milan is the notable exception.
- **Sources:** ISTAT, Immobiliare.it, Banca d'Italia, UBS 2025

### 🇸🇪 Sweden
- **Personal defaults:** kr15,000/mo, kr500,000 down
- **Mortgage rate:** 3.5% (Riksbanken reference rate-linked, early 2026)
- **Mortgage term:** 30 years (but amortization requirements mandate partial principal repayment from day one)
- **Tax bucket:** B (partial deduction). ⚠️ Mortgage interest IS deductible at 30% of interest paid. This is a genuine, significant deduction. Sweden also taxes imputed rental income on high-value properties — not modeled.
- **Capital gains:** 22% rate. Primary residence has a deferral mechanism (uppskov) — not modeled, treated as taxable.
- **Investment return:** 8.0%
- **National market:** Appreciation 2.0% (5yr — includes 2022-23 correction), rent growth 3.0%, property tax 0.30% (capped at kr9,287/yr for houses), insurance 0.20%
- **Closing/selling:** 5% / 3%
- **Key notes:** Swedish amortization requirements (mandatory principal repayment) make the mortgage a stronger forced-savings vehicle than in countries where interest-only periods are possible. The property tax is technically a "municipal fee" (kommunal fastighetsavgift) capped at ~kr9,000/yr.
- **Sources:** Svensk Mäklarstatistik, SCB, Riksbanken

### 🇨🇭 Switzerland
- **Personal defaults:** CHF 3,000/mo, CHF 200,000 down
- **Mortgage rate:** 2.5% (10yr fixed, SNB/Wüest Partner, early 2026)
- **Mortgage term:** 25 years
- **Tax bucket:** B (complex — marginalTax set to 0 as simplification). ⚠️ Switzerland taxes imputed rental income (Eigenmietwert) as ordinary income, but mortgage interest and maintenance costs are deductible against it. Net effect is roughly neutral to slightly favorable for buyers. Varies dramatically by canton and income level.
- **Capital gains:** 0% on primary residence in most cantons (cantonal Grundstückgewinnsteuer may apply on short holds).
- **Investment return:** 7.0%
- **National market:** Appreciation 3.0% (5yr), rent growth 2.0%, property tax 0.10%, insurance 0.20%
- **Closing/selling:** 5% / 3%
- **Key notes:** Swiss mortgages are structurally different — typical to carry mortgage debt indefinitely rather than paying it off, because the tax deduction for interest incentivizes it. The model assumes standard amortization, which may overstate equity buildup vs. Swiss practice. Minimum 20% down payment is regulatory (FINMA).
- **Sources:** BFS/FSO, SNB, Wüest Partner, UBS RE Bubble Index 2025

### 🇯🇵 Japan ⚠️ Structural Disclaimer
- **Personal defaults:** ¥150,000/mo, ¥5,000,000 down
- **Mortgage rate:** 1.5% (35yr fixed via Flat 35, BoJ data, early 2026 — rates rising from near-zero)
- **Mortgage term:** 35 years (Flat 35 is a government-backed fixed-rate product)
- **Tax bucket:** A (a housing loan deduction/tax credit exists but is capped and temporary — not modeled)
- **Capital gains:** 20% rate. ¥30,000,000 special deduction on primary residence sale.
- **Investment return:** 6.0% (Nikkei + global; lower domestic returns historically)
- **National market:** Appreciation 5.0% (5yr — heavily skewed by Tokyo/Osaka), rent growth 2.0%, property tax 1.40% (fixed assets tax + city planning tax combined), insurance 0.10%
- **Closing/selling:** 7% / 5%
- **⚠️ STRUCTURAL DISCLAIMER:** Japanese buildings depreciate by law — 22yr for wooden structures, 47yr for reinforced concrete. Land retains or gains value but structures go to zero. The model assumes total property appreciation, which is ONLY valid for urban condos in Tokyo/Osaka where land value dominates the property. For detached houses, especially outside major cities, the model significantly overstates buyer wealth. Agent commissions are regulated at 3% + ¥60,000.
- **Sources:** MLIT, Land Institute of Japan, E-Housing, Tokyo Portfolio Q1 2025, Global Property Guide

### 🇸🇬 Singapore ⚠️ Structural Disclaimer
- **Personal defaults:** S$3,500/mo, S$200,000 down
- **Mortgage rate:** 3.5% (floating, MAS data, early 2026)
- **Mortgage term:** 25 years (max 30yr, but loan tenure limits based on age)
- **Tax bucket:** A (no deduction)
- **Capital gains:** 0% (no CGT in Singapore)
- **Investment return:** 7.0%
- **National market:** Appreciation 4.0% (5yr, URA PPI), rent growth 3.0%, property tax 0.40% (owner-occupied rates: 0-16% progressive on annual value), insurance 0.20%
- **Closing/selling:** 4% / 3%
- **⚠️ STRUCTURAL DISCLAIMER:** Singapore's housing market is dominated by HDB public housing (~80% of residents). HDB has its own pricing rules, resale restrictions, eligibility criteria, and mandatory CPF (pension fund) usage that this model cannot capture. Results shown are for PRIVATE CONDOMINIUMS only. Foreign buyers face 60% Additional Buyer's Stamp Duty (ABSD) — not modeled. Even for PRs, ABSD is 5% on second property. Use results directionally only.
- **Sources:** URA, HDB, PropertyGuru, MAS

### 🇰🇷 South Korea ⚠️ Structural Disclaimer
- **Personal defaults:** ₩1,500,000/mo, ₩100,000,000 down
- **Mortgage rate:** 4.0% (fixed, Bank of Korea benchmark, early 2026)
- **Mortgage term:** 30 years
- **Tax bucket:** A (a temporary mortgage interest deduction exists for some long-term borrowers but is limited and not modeled)
- **Capital gains:** 0% effective on primary residence under current policy (complex holding period and price-based exemptions).
- **Investment return:** 7.0%
- **National market:** Appreciation 3.0% (5yr), rent growth 3.0%, property tax 0.20% (jaesanse — low on single-home owners), insurance 0.10%
- **Closing/selling:** 5% / 3%
- **⚠️ STRUCTURAL DISCLAIMER:** South Korea's jeonse deposit system (large lump-sum deposit instead of monthly rent, returned at lease end) is unique globally and fundamentally changes the rent-vs-buy calculus. This model assumes standard monthly rent, which does not capture jeonse dynamics. Heavy government intervention (DTI limits, LTV caps, multiple-home acquisition taxes of 8-12%, comprehensive real estate taxes on high-value properties) also materially affects outcomes. Use results directionally only.
- **Sources:** KOSIS, KB Kookmin Bank HPI, Bank of Korea

---

## Metro-Level Data Sources

All 54 metros use location-specific data for appreciation (5yr and 10yr CAGR), rent growth, effective property tax rate, and insurance rate. National averages are used as fallbacks. Sources per country:

| Country | Appreciation Source | Rent Source | Prop Tax Source | Insurance Source |
|---|---|---|---|---|
| US | FHFA HPI purchase-only | Zillow ZORI | Census ACS / LendingTree | NerdWallet / Bankrate |
| CA | CREA / Teranet HPI | CMHC Rental Market Report | Municipal assessment data | IBC |
| UK | ONS UK HPI | Rightmove / HomeLet | VOA Council Tax bands | ABI |
| AU | CoreLogic | SQM Research | State revenue offices | QBE / APRA |
| DE | Destatis | ImmoScout24 | Grundsteuer (by state) | GDV |
| IE | CSO RPPI | Daft.ie | Revenue.ie LPT | Insurance Ireland |
| NZ | REINZ HPI | MBIE tenancy data | Council rates data | ICNZ |
| FR | INSEE RPPI | SeLoger / Notaires | Taxe foncière by commune | FFA |
| ES | INE HPI | Idealista | IBI by municipality | ICEA |
| NL | CBS Statline | Pararius | WOZ-waarde based | Verbond |
| IT | ISTAT | Immobiliare.it | IMU (exempt on prima casa) | ANIA |
| SE | Svensk Mäklarstatistik | SCB | Kommunal avgift | Svensk Försäkring |
| CH | Wüest Partner | BFS/FSO | Cantonal Liegenschaftssteuer | SVV |
| JP | MLIT / Land Institute | E-Housing | Fixed assets tax registers | GIAJ |
| SG | URA PPI | PropertyGuru | IRAS property tax tables | GIA |
| KR | KB Kookmin Bank HPI | KOSIS | Jaesanse registers | KIDI |

---

## Parameter Interaction Notes

Some parameters interact in ways that aren't obvious:

1. **Investment Return vs. Mortgage Rate (the "spread"):** This is the dominant first-order variable. When investReturn > mortgageRate, the renter's portfolio grows faster than the buyer's equity (in percentage terms). When the spread is negative (rare historically), buying is almost always better.

2. **Appreciation + Leverage:** A 4% appreciation on a home with 20% down is effectively a 20% return on the equity invested. This is why appreciation is so powerful — but it works both ways. A -4% year (which happens) wipes out the entire down payment.

3. **Rent Growth vs. Fixed Mortgage:** Over time, the mortgage payment stays fixed while rent grows. Even if renting starts cheaper, the cost crossover is inevitable if rent grows faster than 0%. After the crossover, the buyer invests the surplus — this is the "symmetric surplus investing" feature.

4. **Discipline × Holding Period:** These interact nonlinearly. Low discipline + short hold = renting almost never wins. High discipline + long hold = renting often wins early but buying catches up after mortgage payoff. The inflection depends on all other variables.

5. **SALT Cap × Marginal Tax Rate × Home Price:** The tax benefit is max(0, Interest + min(PropTax, SALT Cap) + Other − StdDeduction) × MarginalRate. With the new $40,400 SALT cap, more homeowners will itemize, increasing the tax benefit. But for homes under ~$300K in low-tax states, the standard deduction still exceeds itemized deductions, and the tax benefit is $0.

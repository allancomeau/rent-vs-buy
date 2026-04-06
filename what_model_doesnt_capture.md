# What This Model Doesn't Capture

Every financial model is a simplification. This document lists what the Rent vs. Buy simulation intentionally omits, why it was omitted, and which direction the omission biases the result (favors buying, favors renting, or neutral). Being explicit about limitations is how the model earns trust.

---

## What v3 Now Addresses (Previously Omitted)

These items were limitations in earlier versions but are now handled by v3:

### Symmetric Surplus Investing ✅ Now Modeled
- **Previously:** Most calculators (and v1 of this model) only tracked the renter's investment portfolio. The buyer's freed-up cash after mortgage payoff was ignored.
- **v3:** Both buyer AND renter invest surplus cash. Whoever has lower housing costs in a given year invests the difference (adjusted by discipline %). After mortgage payoff (~year 30), the buyer's costs drop sharply and they begin accumulating an investment portfolio. This is a first-order wealth driver that most online calculators miss entirely.

### Sensitivity Analysis ✅ Now Modeled
- **Previously:** The verdict was presented as a single number with no sense of confidence.
- **v3:** Sensitivity tornado chart and table show the top 6 variables ranked by impact. The Bottom Line callout rates verdicts as 🟢 Robust, 🟡 Fragile, or 🔴 Highly assumption-dependent based on whether variable swings exceed the advantage. Users can immediately see whether the result is trustworthy or a coin flip.

### International Tax Treatment ✅ Now Modeled
- **Previously:** The model assumed US tax rules for all users, overstating the mortgage interest deduction benefit in countries where it doesn't exist.
- **v3:** 16 countries with correct tax treatment. Bucket A countries (CA, UK, AU, DE, IE, NZ, FR, ES, IT, JP, SG, KR) correctly produce $0 tax benefit. Bucket B countries (NL, SE) apply their partial deductions. Structural disclaimers flag Japan (building depreciation), Singapore (HDB), and South Korea (jeonse).

### Renter Discipline ✅ Now Modeled
- **Previously:** The model assumed the renter invested 100% of surplus — a theoretical ideal contradicted by behavioral research.
- **v3:** Adjustable discipline slider (30–100%) with clear guidance that 60–75% is realistic. Bernstein & Koudijs (QJE 2024) showed mortgage payments function as forced savings at ~100% efficiency; renters rarely match this. This single variable often flips the verdict.

---

## Omissions That Favor Buying (Model Understates Buyer Advantage)

### Refinancing Optionality
- **What it is:** If rates drop, the buyer can refinance to a lower payment. The renter has no equivalent — rent doesn't drop when rates do.
- **Why omitted:** Refinancing requires predicting future rate paths, which is speculative. Adding it as a toggle (planned for v4) is cleaner than baking in assumptions.
- **Magnitude:** Significant. A buyer who locked in at 7% and refinanced to 5% would save ~$400/mo on a $320K loan — surplus that gets invested.
- **Directional bias:** Omission favors renting. The buyer has a free option that the model ignores.

### Imputed Rent (Shelter Value)
- **What it is:** The owner-occupier consumes housing services worth the market rent but doesn't pay themselves for it. Jordà-Schularick-Taylor found this adds ~3.5–5% annually to housing's total return.
- **Why omitted:** Our model captures this indirectly — the buyer's "cost" already excludes rent (they live in the home), while the renter pays rent. The wealth comparison at sale implicitly accounts for shelter consumed. Including imputed rent as an explicit return would double-count it.
- **Directional bias:** Neutral in our framework, but worth noting because other models that compare "housing returns vs equity returns" without imputed rent systematically understate housing.

### HELOC / Home Equity Access
- **What it is:** Homeowners can borrow against equity (HELOC, cash-out refinance) to invest or cover emergencies. Renters have no equivalent collateral.
- **Why omitted:** Requires modeling a second loan with its own rate, draw schedule, and repayment — significant complexity for a feature that depends heavily on individual behavior.
- **Magnitude:** Moderate. A HELOC at 7% invested at 10% generates a positive spread, but the risk is real (you're leveraging your home).
- **Directional bias:** Omission slightly favors renting.

### Forced Savings Beyond Mortgage Principal
- **What it is:** Homeowners often make home improvements that increase value — effectively forced investment. The model only captures mortgage principal as forced savings.
- **Why omitted:** Home improvement ROI varies wildly (kitchen remodel ~75% recovery, pool ~40%). Too speculative to generalize.
- **Directional bias:** Slight favor to renting, since real-world buyers build more equity through improvements than the model shows.

### Emotional and Behavioral Benefits of Ownership
- **What it is:** Stability, community ties, ability to customize, no landlord risk, psychological ownership effect. These have real economic value (lower moving frequency, better school outcomes for children, etc.) but aren't financial in the narrow sense.
- **Why omitted:** Not quantifiable in a financial model. Documented here so the family can weigh them alongside the numbers.
- **Directional bias:** Favors buying in ways the model can't capture.

---

## Omissions That Favor Renting (Model Understates Renter Advantage)

### PMI (Private Mortgage Insurance)
- **What it is:** Required when down payment is below 20%. Typically 0.3–1.5% of the loan balance annually, removed when equity reaches 20%.
- **Why omitted:** The model defaults to 20% down, which avoids PMI. If the user enters a lower down payment, the model understates buyer costs.
- **Magnitude:** On a $320K loan with 10% down, PMI at 0.8% = ~$2,560/year until equity reaches 20%. Over 5–7 years, that's $13K–$18K in unrecoverable costs.
- **Directional bias:** Omission favors buying when down payment is below 20%.
- **Note:** Starting in 2026 (OBBBA), PMI on acquisition debt is treated as deductible mortgage interest, partially offsetting this cost. Not yet modeled.

### Liquidity Risk
- **What it is:** Home equity is illiquid — you can't sell a bedroom to cover an emergency. The renter's portfolio can be partially liquidated at any time.
- **Why omitted:** Liquidity has option value, but quantifying it requires assumptions about emergency probability and severity that are too personal to generalize.
- **Directional bias:** Favors renting. The renter's wealth is more accessible.

### Concentration Risk
- **What it is:** The buyer's wealth is concentrated in a single asset (one house, one neighborhood, one city). The renter's portfolio is diversified across hundreds of companies. Modern portfolio theory says diversification is a free lunch.
- **Why omitted:** The model uses a single appreciation rate, which implicitly assumes the specific home tracks the metro average. In reality, individual homes can significantly underperform or outperform the metro index.
- **Magnitude:** Individual home returns have much higher variance than the metro-level indices suggest. A home near a new highway or in a declining school district can lose value while the metro average rises.
- **Directional bias:** Favors renting on a risk-adjusted basis.

### Geographic Flexibility
- **What it is:** Renters can relocate for career opportunities with minimal friction (~1 month's rent to break a lease). Buyers face 6–9% transaction costs and months of selling process.
- **Why omitted:** Career mobility value depends entirely on the individual's industry, career stage, and personal preferences.
- **Magnitude:** For someone in their mid-to-late twenties in a mobile career (tech, consulting, finance), this can be worth tens of thousands in higher lifetime earnings.
- **Directional bias:** Favors renting, especially for younger workers.

### Maintenance Variance and Major Repairs
- **What it is:** The model assumes a steady 1%/year maintenance cost. Real maintenance is lumpy — a $15K roof replacement in year 12, a $10K HVAC failure in year 8, nothing in between.
- **Why omitted:** Smoothing maintenance into an annual rate is standard practice (Poterba δ). Modeling individual repair events would require assumptions about home age and condition.
- **Magnitude:** In any given year, actual maintenance can be $0 or $20K+. The 1% average is well-supported empirically but hides significant variance.
- **Directional bias:** Slightly favors buying in the model, since smooth costs look more manageable than lumpy surprises.

### Rental Market Protections
- **What it is:** Some markets have rent control, rent stabilization, or strong tenant protections that limit rent growth below the model's assumption.
- **Why omitted:** Rent control varies dramatically by jurisdiction and is politically unstable (can be enacted or repealed). The model uses market-rate rent growth.
- **Directional bias:** In rent-controlled markets, the model overstates rent growth — favoring buying more than reality would.

---

## Omissions That Are Directionally Neutral or Context-Dependent

### State Income Tax Interaction with SALT
- **What it is:** The SALT deduction cap ($40,400 for 2026) applies to property tax + state income tax combined. The model only includes property tax in the SALT calculation.
- **Why omitted:** State income tax varies by state and income level. Users in high-income-tax states (CA, NY, NJ) may already be hitting the SALT cap from income tax alone, making the property tax portion non-deductible.
- **Workaround:** Users can approximate state income tax by adjusting the "Other Itemized Deductions" field upward.
- **Directional bias:** In high-tax states, the model may overstate the tax benefit (property tax appears deductible when the SALT cap is already consumed by income tax). In low-tax states, no impact.

### HOA Fees
- **What it is:** Monthly fees for condos and some planned communities. Can range from $100–$800+/month.
- **Why omitted:** Applies only to certain property types. Including it as a default would bias results for single-family home buyers (the majority).
- **Workaround:** Users can approximate HOA by reducing their monthly payment input (since HOA comes out of the same housing budget).
- **Directional bias:** Omission favors buying for condo purchasers.

### Moving Costs
- **What it is:** Physical costs of relocating — movers, deposits, overlap rent, temporary housing. Applies to both buyer and renter.
- **Why omitted:** Moving costs affect both paths (the renter moves into a rental, the buyer moves into a home). They roughly cancel out at the start. At sale, the buyer has additional moving costs, but these are small relative to selling costs already modeled.
- **Directional bias:** Approximately neutral.

### Tax Law Changes
- **What it is:** The SALT cap reverts to $10,000 in 2030 unless extended. The mortgage interest deduction limit could change. Capital gains rates and exclusions may be modified by future legislation.
- **Why omitted:** Predicting future tax law is speculative. The model uses current (2026) tax parameters.
- **Directional bias:** Unknown. The SALT cap reversion to $10,000 would reduce buyer tax benefits in high-tax states.

### Inflation's Effect on Debt
- **What it is:** Inflation erodes the real value of fixed mortgage debt. A $2,000/mo payment in 2026 dollars is worth much less in 2041 dollars. This is a hidden benefit of long-term fixed-rate debt.
- **Why omitted:** The model runs in nominal dollars throughout, which implicitly captures this — the mortgage payment stays fixed while everything else grows. The "cost crossover" chart shows this directly.
- **Directional bias:** Properly captured in nominal terms. If the model were in real dollars, this would need explicit adjustment.

### Investment Portfolio Composition
- **What it is:** The model assumes a single return rate for all invested cash. Real portfolios change over time (target-date funds shift from equities to bonds as the investor ages).
- **Why omitted:** Portfolio strategy is deeply personal. The model lets users set any return rate they want.
- **Workaround:** Use a blended rate — e.g., 8% instead of 10% — to approximate a 70/30 stock/bond portfolio.
- **Directional bias:** The 10% default assumes 100% equities, which is aggressive. If the renter would actually hold a more conservative portfolio, the model overstates renter wealth.

---

## Summary: Net Directional Bias

Tallying the remaining omissions (after v3 improvements):

| Direction | Count | Key items |
|-----------|-------|-----------|
| Favors buying (model understates buyer advantage) | 5 | Refinancing optionality, HELOC access, home improvements, forced savings beyond principal, emotional/stability value |
| Favors renting (model understates renter advantage) | 6 | PMI below 20%, liquidity risk, concentration risk, career flexibility, maintenance variance, rent control |
| Neutral / context-dependent | 6 | SALT interaction, HOA, moving costs, future tax law changes, inflation on debt (captured implicitly), portfolio composition |

**v3 improvements that narrowed the gap:** Symmetric surplus investing (now correctly captures buyer's post-payoff investment growth), renter discipline toggle (removes the unrealistic "perfect renter" assumption), international tax handling (no longer overstates deduction in 13 countries), and sensitivity analysis (quantifies verdict confidence).

**Net assessment:** The remaining omissions roughly balance out for the "typical" case. However, for specific situations:
- **Young, mobile, career-building renters** benefit from flexibility and liquidity the model can't capture → model slightly overstates buying's appeal for this demographic.
- **Settled buyers with stable careers and behavioral discipline challenges** benefit from forced savings and refinancing optionality → model slightly overstates renting's appeal for this demographic.
- **Non-US users** should note that country-specific programs (Canada's FHSA/HBP, NZ's KiwiSaver, UK's Help to Buy, Australia's First Home Owner Grant) are not modeled and generally favor buying.

The user should read the verdict alongside these omissions and ask: "Which uncaptured factors apply to me?"

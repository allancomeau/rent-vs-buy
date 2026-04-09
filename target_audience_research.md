# The Rent-vs-Buy Decision: Who Needs It, What Matters, and Where the Model Fits

Research document for the Rent vs. Buy Financial Simulation Engine. Covers first-time homebuyer demographics, down payment behavior, PMI relevance, sensitivity analysis, tax deduction applicability, and geographic variation — all scoped to the tool's target audience (mid-to-late twenties, $60K-$130K household income).

Generated from deep research, April 2026. See the full cited version in the project's Claude conversation history.

---

## Key Findings

### 1. Target Audience Profile
- First-time buyers: 21% of all purchases (NAR 2025), median age 33-35 (AEI/Census)
- Median first-time buyer household income: $97K (NAR 2024), $134K among ages 25-34 (HMDA)
- 32% say saving down payment is the hardest step. 27% of ages 26-34 received family help.
- 64% of Gen Z adults spend >30% of income on housing

### 2. Down Payment Reality
- Median first-time buyer: 9-10% down (NAR 2024-2025)
- Ages 25-34: 43% put down ≤5%, 60% put down ≤10%, only 21% exceed 20%
- ~79% of first-time buyers in this bracket pay mortgage insurance (PMI or FHA MIP)
- Loan type split: 65% conventional, 23% FHA, 10% VA

### 3. PMI Is the Default, Not the Exception
- PMI rates range 0.15%-1.05% annually depending on credit score and LTV
- Credit score has ~7x more impact on PMI cost than down payment amount
- On a $350K home with 5% down and 700 FICO: ~$114/mo PMI for ~8-10 years
- FHA MIP: flat 0.55% annually + 1.75% upfront, cannot be removed if <10% down
- Ignoring PMI understates buying costs by $840-$3,500+/year for the target demographic

### 4. What Drives the Outcome (Sensitivity Ranking)
1. Home appreciation rate — single most sensitive variable
2. Time horizon — breakeven at current rates: 5-7+ years
3. Investment return assumption — renter's counterpart to appreciation
4. Mortgage rate — $207/mo difference per 1% on a $315K loan
5. Rent growth rate — creates massive cumulative divergence

### 5. Renter Discipline Is the Behavioral Fault Line
- Beracha & Johnson (2012): renting wins with full reinvestment, buying wins without it
- 41% of renters have saved nothing for retirement
- Median renter net worth: $10,400 vs $400,000 for homeowners (38:1 ratio)
- "If you're a good saver, rent. If not, buy." — PWL Capital

### 6. Tax Deductions Are Largely Irrelevant for This Audience
- Only ~11% of all taxpayers itemize post-TCJA
- On a $400K home with 20% down at 6.5%, MFJ tax savings: ~$36/year
- Single filers benefit more: ~$2,800/year on a $350K home with 10% down
- The "Other Itemized" input has minimal relevance for mid-to-late twenties users

### 7. Geographic Variation
- National price-to-rent ratio: ~14.3 (2024)
- Renting favored: San Jose (37-45), SF (~36), Seattle (~36), Boston (~25)
- Buying favored: Cleveland (11.0), Detroit (~8), Pittsburgh (9.6)
- In 48 of 50 most populous US cities, renting is cheaper monthly (Bankrate 2025)

---

## Model Implications

### Added in v3.9.9 (Display-Only Insights)
- Down payment % target with gap/surplus display
- PMI estimate based on simplified rate table
- Income reality check (28% front-end ratio)
- Round-trip transaction cost summary

### Future Consideration (Engine Changes)
- Full PMI modeling with credit score tiers and automatic removal at 80% LTV
- FHA vs conventional toggle
- Savings discipline visualization (dual-scenario)

### Correctly Handled Already
- Renter discipline slider (30-100%, 60-75% realistic guidance)
- Sensitivity tornado showing which assumption matters most
- Transaction costs in engine (closingPct, sellingPct)
- Tax treatment (excess itemization, post-TCJA correct)

---

Sources: NAR (2024, 2025), HMDA 2024, AEI Housing Center, Federal Reserve SCF/SHED, Bankrate, Harvard JCHS, Beracha & Johnson (2012, 2017), Ben Felix/PWL Capital, NerdWallet, Tax Policy Center, Urban Institute, Down Payment Resource, MGIC/Radian PMI rate cards.

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
| Implied volatility drag (½σ²) | **0.35%** | **1.70%** |
| Nominal price-only arithmetic | **3.54%** | 6.70% |
| Price-only σ | **8.24%** | **18.22%** |

The FHFA House Price Index shows a nominal CAGR of **~4.3%** for price appreciation from 1991–2026. The National Association of Realtors cites roughly **5.5%** nominal appreciation over the past 50 years, though this figure is inflated by quality improvements in housing stock over time (larger homes, better amenities) that repeat-sales indices like Case-Shiller and FHFA control for.

Housing's dramatically lower volatility produces a critical consequence: **the gap between arithmetic and geometric means for housing is tiny compared to equities**. With σ ≈ 8%, housing's volatility drag is only ½ × (0.08)² = **0.32%**, versus 2.0% for stocks. This means that on a CAGR basis — what actually matters for wealth accumulation — housing's disadvantage relative to equities narrows meaningfully. Jordà et al. found that globally, housing's geometric mean total return of **6.61%** real actually *exceeded* equities' **4.64%** real, despite equities having higher arithmetic means. Housing Sharpe ratios exceeded equity Sharpe ratios in every one of the 16 countries studied. For the U.S. specifically, equities still outperformed housing on both arithmetic and geometric bases, but the gap was smaller in geometric terms.

## What financial planning tools actually do — and often get wrong

The unanimous consensus among authoritative sources is clear on the core principle: **use the geometric mean for deterministic (straight-line) projections, and the arithmetic mean for stochastic (Monte Carlo) simulations.** Kitces states it definitively: "When doing a traditional 'straight-line' retirement projection… the geometric return IS the appropriate return to use." Professional financial planning platforms — NaviPlan, RightCapital, Voyant, and Boldin — all implement this distinction explicitly in their documentation. NaviPlan even converts geometric inputs to arithmetic equivalents internally before running Monte Carlo.

The logic is straightforward. The arithmetic mean is the correct expected return for any single year. But in a Monte Carlo simulation, the process of drawing random returns from a volatile distribution automatically creates the variance drain. If you fed the geometric mean into a Monte Carlo engine, you would "double-count" volatility — once through the lower input rate and again through the simulated randomness — producing overly pessimistic results. Conversely, feeding the arithmetic mean into a deterministic model removes all randomness while baking in the single-period expected value, overstating the compound result because it ignores the drag that volatility would have imposed.

Consumer-facing rent-vs-buy calculators almost universally use deterministic compounding. The New York Times calculator defaults to roughly **4%** for investment returns, Zillow uses **6%**, and most others allow user input between 4–7%. None of these tools run Monte Carlo internally. Since they compound deterministically, the geometric mean is the theoretically correct input — and their relatively conservative default rates (well below 10%) suggest an implicit awareness of this, even if the documentation rarely specifies which mean is intended.

When someone says "stocks return 10% per year," they are almost always citing the geometric mean (CAGR). The arithmetic mean of ~12% rarely appears in popular discourse. This is actually the correct instinct for most contexts: the 10% figure is what a dollar actually grew to over time, compounded. The confusion arises when financial models need explicit assumptions: a well-meaning planner might enter "10%" thinking it is conservative, not realizing it already reflects the CAGR and is the appropriate input for straight-line projections — or they might enter "12%" from an academic source, not realizing this arithmetic figure will overstate results in a deterministic model.

## Jacquier, Kane, and Marcus: the truth lies between the two means

The deepest academic treatment comes from Jacquier, Kane, and Marcus's 2003 *Financial Analysts Journal* paper, "Geometric or Arithmetic Mean: A Reconsideration." Their key finding adds an important nuance: **neither mean alone produces an unbiased forecast of terminal wealth**. Compounding at the arithmetic mean yields an upwardly biased forecast because of estimation error in the sample mean. Compounding at the geometric mean yields a downwardly biased forecast. The unbiased estimate uses a weighted average: **μ* = (1 − H/T) × Arithmetic + (H/T) × Geometric**, where H is the investment horizon and T is the sample period. For short horizons relative to the data history, the arithmetic mean dominates. As the horizon approaches the length of the historical sample, the geometric mean takes over entirely. For a 40-year retirement projection using ~100 years of data, the optimal rate sits roughly **60% geometric and 40% arithmetic** — splitting the difference between approximately 10% and 12%, landing near **10.8%** for a nominal equity return assumption.

This is a practically important result. Over 40 years, the difference between compounding at 10%, 10.8%, and 12% on $100,000 is **$4.5 million, $6.2 million, and $9.3 million**, respectively. The Jacquier et al. framework suggests the median planner is making a roughly correct assumption with "10%," slightly conservative relative to the unbiased estimator but far better than the 12% arithmetic figure.

## Conclusion

The volatility drag formula g ≈ a − σ²/2 is one of the most practically important relationships in personal finance, yet it remains poorly understood outside of quantitative circles. For U.S. equities, it explains precisely why the "10% return" and the "12% return" are both correct — they are simply different statistical summaries of the same data, separated by exactly the amount that ~20% annual volatility would predict. For housing, the same formula explains why low-volatility assets preserve more of their arithmetic return through compounding: housing loses only ~0.3% to volatility drag versus ~2% for stocks.

The actionable rule is simple: **compound at the geometric mean for deterministic projections; use the arithmetic mean only if your model explicitly simulates randomness.** For a rent-vs-buy calculator or a straight-line retirement projection, 10% nominal (or ~7% real) is the right equity assumption. Using 12% in such a model does not represent optimism — it represents a mathematical error that overstates terminal wealth by 50–70% over a multi-decade horizon. And for housing price appreciation specifically, the correct nominal input for deterministic compounding is roughly **3.5–4.5%** (price only) — a figure that makes equities look far more attractive than the misleading comparison of "10% stocks vs. 5% housing" that often appears in popular financial advice.
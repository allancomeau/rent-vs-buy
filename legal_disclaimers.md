# Legal disclaimers for your open-source rent-vs-buy calculator

Your risk profile is low — a free, open-source, user-input-driven educational calculator that covers real estate (not securities) and generates no revenue fails virtually every legal test for "financial advice" under US law. **The single biggest practical risk is your employer's compliance department, not regulators.** Proactively disclosing the project to your firm's compliance team before posting publicly is the most important step you can take. Below is a complete, practical guide to disclaimers and risk mitigation.

## The legal tests are strongly in your favor

Under the Investment Advisers Act of 1940, the SEC uses a three-prong "ABC" test to define an investment adviser: the person must (A) advise about **securities**, (B) do so as a **business**, and (C) receive **compensation**. Your calculator fails all three prongs. It addresses real estate decisions, not securities. It's a free side project, not a business. And it generates zero revenue. The Supreme Court's *Lowe v. SEC* (1985) further protects general financial commentary not tailored to individual circumstances under the "publisher's exclusion."

FINRA Rule 2210 (Communications with the Public) applies only to communications about **a firm's products or services**. Regulatory Notice 17-18 explicitly clarified that associated persons' personal communications are not subject to Rule 2210 when the content is unrelated to the firm. Better still, Rule 2210(d)(1)(F)(i) contains a specific carve-out for **"a hypothetical illustration of mathematical principles"** — a calculator where users input their own assumptions and see estimated outputs is the textbook example of this exemption.

New Hampshire follows the Uniform Securities Act, which mirrors the federal three-prong test. The state's definition of an investment adviser under RSA 421-B:4-403 requires compensation for advice about securities — neither condition applies here.

**The bottom line**: no SEC registration, no FINRA rule violation, and no state-level trigger for a free, open-source, non-securities educational calculator. But strong disclaimers are still essential as a defensive layer.

## "Not financial advice" alone is legally inadequate

A bare "this is not financial advice" disclaimer provides minimal legal protection. Courts can still find liability depending on jurisdiction, negligence, or harm caused. FINRA's own calculator tools use a multi-layered disclaimer covering six distinct elements. Your disclaimers should cover all of these components:

- **Purpose statement**: "For educational and informational purposes only — does not constitute financial, investment, tax, or legal advice."
- **No professional relationship**: "Use of this tool does not create an advisor-client, fiduciary, or professional relationship. The creator is not a registered investment adviser, broker-dealer, or financial planner."
- **Hypothetical nature**: "All results are hypothetical and based entirely on user-provided assumptions. Actual outcomes will vary significantly."
- **Professional referral**: "Consult a qualified financial adviser, tax professional, or attorney before making financial decisions."
- **User responsibility**: "Any reliance you place on this tool's output is strictly at your own risk."
- **Employer separation**: "This is a personal project. Views expressed are my own and do not represent those of my employer."

These six elements, combined, create a substantially stronger legal position than any single phrase. FINRA's own model disclaimer for its calculators reads: *"These calculators are designed to be informational and educational tools only, and when used alone, do not constitute investment advice... The results presented by this calculator are hypothetical and may not reflect the actual growth of your own investments."* Mirroring this language from the regulator itself is smart positioning.

## The MIT license is necessary but not sufficient

The MIT License's "AS IS" warranty disclaimer and liability limitation protect against claims that the **software is defective** — bugs, errors, downtime. But it says nothing about **financial advice or decision-making**. There's a meaningful legal distinction between "this software might have bugs" (covered) and "you should not make financial decisions based on this output" (not covered). Most users of a GitHub Pages-hosted calculator will never see the LICENSE file — they interact with the web page.

Research across dozens of open-source financial tools on GitHub confirms this gap. Projects like **cFIREsim** (157 stars), **cgtcalc** (UK tax calculator), and **secalculator** all include separate financial disclaimers beyond their license files. The most common pattern places a `## Disclaimer` section in the README and a visible disclaimer on the web application itself — typically in the footer or a dedicated page.

Your tool needs **three layers of protection**: the MIT LICENSE file for software liability, a Disclaimer section in the README for developer-facing coverage, and a visible on-page disclaimer for end users. The warranty disclaimer should use ALL CAPS per UCC Section 2-316(3) requirements for conspicuous warranty disclaimers.

## Your employer's compliance department is the real risk

Regulatory enforcement against individual open-source developers hosting free educational calculators is essentially unprecedented — regulators focus on commercial actors, finfluencers promoting specific products, and unlicensed advisors collecting fees. Your **employer's internal compliance policies** represent a far more tangible risk.

Even though FINRA Rule 3270 (Outside Business Activities) technically applies only to registered representatives, **many financial services firms extend OBA-like reporting requirements to all employees** via internal policy. Firms commonly require pre-approval for any public activity touching financial topics regardless of role. The creator's CRM/data role doesn't exempt them from company-wide social media and outside activity policies.

Specific employer-related risks include compliance review requirements, potential conflicts of interest if the employer operates in mortgage or housing finance, and reputational concerns about the tool being perceived as firm-endorsed. The safest approach is to **proactively disclose to compliance before posting** — frame it as: "I've built a personal educational tool; it's free, open-source, unrelated to our products; here's my disclaimer language." Most compliance departments will appreciate the transparency and either approve it or suggest modifications. Not disclosing and having compliance discover it later is a far worse outcome.

## What to put on your LinkedIn post

A LinkedIn post sharing the tool needs its own concise disclaimer, not just a link to the tool's disclaimer page. FINRA's social media guidance (Regulatory Notices 10-06, 11-39, 17-18) treats personal communications as generally exempt from Rule 2210, but only when the content clearly doesn't relate to the firm's products or services. Including **employer-separation language** in the post itself removes ambiguity.

Here's a practical template:

> ⚠️ **Disclaimer**: This is a personal educational project — views are my own and do not represent those of my employer. This tool is for informational and educational purposes only and does not constitute financial, investment, tax, or legal advice. Results are estimates based on simplified assumptions. Please consult a qualified professional before making any financial decisions.

Three critical rules for the post itself: **never mention your employer by name**, don't reference employer products or proprietary data, and frame the tool as education ("I built this to help people think through the rent-vs-buy decision") rather than advice ("use this to decide whether to buy"). FINRA Regulatory Notice 19-31 explicitly encourages educational communications that promote financial literacy — lean into this framing.

## International exposure is manageable with the right language

The tool covers **16 countries**, which creates theoretical exposure to multiple regulatory regimes. In practice, the risk is negligible for a free educational calculator, but targeted disclaimer language costs nothing and provides meaningful protection.

The **UK FCA** has the strictest financial promotion rules — Section 21 of FSMA 2000 makes it a criminal offense to communicate a "financial promotion" without authorization, with extraterritorial reach. However, a financial promotion requires an "invitation or inducement to engage in investment activity." A generic educational calculator that doesn't promote specific products, doesn't recommend actions, and isn't provided "in the course of business" almost certainly falls outside this definition.

**Australia's ASIC** provides the most useful framework — their Generic Calculators Instrument explicitly grants relief from licensing requirements for calculators that don't promote specific products, let users alter assumptions, use reasonable defaults, and display clear purpose/limitation statements. Following ASIC's conditions gives you the gold standard for international calculator disclaimers.

For a client-side tool with no data collection, **GDPR largely doesn't apply** — there's no processing of personal data. GitHub Pages doesn't set cookies on public sites by default. However, adding Google Analytics or any third-party tracking scripts would trigger GDPR consent requirements. The simplest approach is to avoid all analytics and include a one-line privacy note: "This tool does not collect, store, or transmit any personal data. All calculations are performed locally in your browser."

A practical international catch-all disclaimer: *"Laws, regulations, tax rules, and real estate practices vary significantly by country and locality. This tool is not intended for residents of jurisdictions where its use would violate local laws or regulations."*

## Recommended complete disclaimer for the tool

Here is a comprehensive, ready-to-adapt disclaimer combining all the elements above — place it on a dedicated Disclaimer page linked from the calculator's footer, with a condensed version visible on each calculator page:

> **Disclaimer**
>
> This tool is provided for **educational and informational purposes only**. It does not constitute financial advice, investment advice, tax advice, legal advice, or any other form of professional advice. Nothing on this website should be construed as a recommendation, endorsement, or offer to buy, sell, or otherwise transact in any property, financial instrument, or investment strategy.
>
> The creator of this tool is **not a registered investment adviser, broker-dealer, financial planner, or tax professional**. Use of this tool does not create an advisor-client, fiduciary, or any professional-client relationship.
>
> All results are **hypothetical estimates based entirely on user-provided assumptions** and simplified models. Actual financial outcomes will vary significantly based on market conditions, interest rates, tax laws, personal circumstances, and factors not captured by this model. Tax rates, transaction costs, and market data are approximations that may not reflect current conditions in all locations.
>
> **Consult a qualified financial adviser, tax professional, or attorney** in your jurisdiction before making any financial decisions, including decisions about buying or renting a home.
>
> This is a **personal project**. Views and tools shared are the creator's own and do not represent those of any employer, past or present.
>
> THIS TOOL IS PROVIDED "AS IS" WITHOUT WARRANTIES OF ANY KIND, EXPRESS OR IMPLIED. IN NO EVENT SHALL THE CREATOR BE LIABLE FOR ANY DAMAGES ARISING FROM YOUR USE OF THIS TOOL. YOU USE THIS TOOL ENTIRELY AT YOUR OWN RISK.

## Conclusion

The legal risk for this specific tool is genuinely low — it fails every prong of the investment adviser test, fits squarely within FINRA's mathematical illustration exemption, and generates no revenue. But disclaimers are cheap insurance. The six-component disclaimer approach (purpose, no relationship, hypothetical nature, consult a professional, user responsibility, employer separation) provides far stronger protection than "not financial advice" alone. The MIT license handles software liability but leaves a gap on financial content that a separate disclaimer must fill. Internationally, following ASIC's calculator framework gives you the most robust protection across jurisdictions. And the single most important action item isn't legal language at all — it's a five-minute conversation with your employer's compliance team before you post anything publicly.
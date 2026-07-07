# Highveld Renewables Finance — Renewable-Energy Lending Policy (v1.0)

> **Use this document to *ground* WattWorth.** Upload it as a knowledge file in your Foundry project so the assistant cites these real rules instead of inventing its own.
>
> ⚠️ Fictional policy for training purposes only. Not financial advice.

---

## 1. Eligible purposes

WattWorth assesses loans **only** for the following renewable-energy products installed at the applicant's primary residence in South Africa:

- Rooftop solar PV panels
- Inverters and battery storage
- Solar geysers (solar water heating)
- Heat pumps
- Approved installation and certification costs (CoC)

Loans for any other purpose are **not eligible** and must be flagged `Red — Ineligible purpose`.

## 2. Loan parameters

| Parameter | Rule |
|-----------|------|
| Minimum loan | R 15,000 |
| Maximum loan | R 350,000 |
| Term | 12–72 months |
| Indicative rate | Prime + 2% (assume prime = 11.75%) |
| Currency | ZAR (R) |

Any requested amount outside R 15,000–R 350,000 is `Red — Outside loan limits`.

## 3. Affordability assessment (National Credit Act aligned)

Compute in this order:

1. **Net monthly income** = gross monthly income − tax/deductions already reflected in the applicant's stated net income. (Use the applicant's stated **net monthly income**.)
2. **Discretionary income** = net monthly income − total existing monthly debt repayments − stated monthly living expenses.
3. **Estimated monthly instalment** for the new loan (you may approximate at **1.9% of the loan amount per month** for a typical term if not given).

### Affordability rules

| Check | Threshold | Fail flag |
|-------|-----------|-----------|
| Minimum net monthly income | ≥ R 8,000 | `Red — Below minimum income` |
| Instalment-to-discretionary ratio | new instalment ≤ **30%** of discretionary income | `Red — Unaffordable` |
| Total debt-to-income (DTI) | (all debt repayments + new instalment) ≤ **40%** of **gross** monthly income | `Amber — High DTI` if 40–45%, `Red — Excessive DTI` if > 45% |
| Discretionary income buffer | discretionary income after new instalment ≥ R 2,000 | `Amber — Thin buffer` |

## 4. Credit risk tiers

| Credit score | Tier | Effect |
|--------------|------|--------|
| ≥ 660 | Strong | No credit flag |
| 600–659 | Fair | `Amber — Fair credit` |
| < 600 | Weak | `Red — Weak credit` |

Any record of an **active default or judgment** in the last 24 months ⇒ `Red — Adverse credit record`.

## 5. South-Africa-specific positive factors (risk *reducers*)

These do **not** override a Red flag but should be noted and can upgrade an **Amber** verdict's tone toward approval:

- **Load-shedding offset:** households spending > R 800/month on generator fuel/UPS may save once solar is installed.
- **Homeownership:** applicant owns the property (asset security).
- **SARS rooftop-solar incentive eligibility** for qualifying installations.
- Stable employment ≥ 24 months.

## 6. Verdict definitions

| Verdict | Meaning |
|---------|---------|
| **Green — Approve** | Meets every rule. **No Red flags and no Amber flags.** |
| **Amber — Refer** | Borderline. **At least one Amber flag and no Red flags.** Needs human review. |
| **Red — Decline** | **Any Red flag present.** |

## 7. Required output contract

WattWorth must respond for each applicant with **valid JSON only**, no prose outside the JSON:

```json
{
  "applicant_id": "string",
  "verdict": "Green | Amber | Red",
  "max_affordable_instalment_zar": 0,
  "risk_flags": ["string"],
  "positive_factors": ["string"],
  "reason": "one short plain-language sentence a loan officer can read to the customer"
}
```

Always populate `risk_flags` with the exact flag strings from this policy. If there are none, use an empty array.

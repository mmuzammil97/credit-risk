# Modelling Credit Risk and Regulatory Frameworks

## Why do banks need to model risk?

Modelling risk is inherently important to a bank as it is something vested in its definition as an institution — banks act as intermediaries between borrowers and lenders. If the bank lends out money to a person, and the person defaults, the bank would not be able to pay the lender back.

This perhaps is the crux of the problem. Therefore, banks need to do *something* in the case the person they lent the money to defaults.

What the bank can do here is to hold out capital against the loan it gives out. For instance, if it is giving out a loan to a person the bank deems risky, then the bank must have  at least the same fraction of cash against it, in case the person does default. The more risky the party whom the bank is lending money to, the more capital they will have to hold against the money lent.

Since people's hard earned money is involved, this needs to be regulated — that is, we need to demarcate rigorously how much capital has to be held for a given level of risk profile. For that, we had the Basel Accords.

**Basel I** was somewhat crude. The banks were just asked to hold 8% capital against the risk weighted assets; all corporate loans were equal in terms of risk the bank had taken on. **Basel II** then revisited this in 2004, including the necessity to actually compute the risk the bank takes on for each loan it gives out. Banks could either have a standardised, or an internal ratings-based system to decide how much capital they would have to hold against a loan they had to give out.

However, over time, as financial products and their derivatives became more sophisticated, systemic risks grew. Perhaps institutions were not aware of how to compute the risk they were taking on in trading these risky derivative products. One of these products was a mortgage-backed security (MBS) — a derivative security that derived its value from underlying mortgages. As trading volumes of these MBS increased, more and more were constructed with underlying mortgages being subprime (risky). Soon MBS trading grew extensively high, and the people with those subprime mortgages underlying these securities started to default. It turned out many ratings agencies (Moody's, S&P) were also rating risky loans as less risky. The whole system was taking on a huge collective risk, and then in 2008, as people started defaulting more and more, and one of the banks failed, it all came crumbling down. People lost confidence in the banks. Wall Street was in turmoil, and the rest is history.

**Basel III** was then introduced as a tighter regulatory framework for banks post-2008 — to prevent another economic breakdown.

The major governing idea for any regulatory framework is what a bank's expected loss is on a loan it gives out, given as:

$$
EL = PD × EAD × LGD
$$

Where:
- **PD** — Probability of Default
- **EAD** — Exposure at Default
- **LGD** — Loss Given Default

This expected loss is provisioned for in the income statement, but the market fluctuates and the tail risks can be significantly different from what is expected. The capital allocated as a safety net thus shields against any **unexpected losses**.

Under Basel II, banks had a choice of either a standardised rating system (per loan type), or an **Internal Ratings-Based (IRB)** system. In the Standardised approach, for instance, all mortgage loans had a risk weight of 35%, and 8% worth of capital had to be secured against it — however, the 35% rating was ubiquitous, regardless of the risk profile of the borrower. This meant banks often had to secure more capital than needed for 'safe' borrowers. Banks thus favour an IRB system that allows them to develop models that determine the risk profile of a borrower and judiciously assign how much capital to hold against it, maintaining the subtle balance between being safe from defaults while also maximising profits from lending.

There are two variants of IRB:
- **Foundation IRB (F-IRB)**: banks estimate only PD; the regulator prescribes LGD and EAD.
- **Advanced IRB (A-IRB)**: banks estimate all three components internally, allowing for the most risk-sensitive capital calculations.

These internal ratings are not arbitrary — they are carefully vetted and must be tested internally for at least 3 years before a financial regulatory board approves them for use on real capital. In Canada, this is the **Office of the Superintendent of Financial Institutions (OSFI)**.

---

## How does our model fit into the Internal Ratings-Based system?

The model developed in [`credit-risk.ipynb`](credit-risk.ipynb) estimates the probability of default within 12 months, placing it within the **F-IRB** framework. The 12-month PD horizon is the standard definition under Basel II.

We develop a logistic regression model on home loan data from the [Home Credit Default Risk Kaggle competition](https://www.kaggle.com/competitions/home-credit-default-risk/data). While more sophisticated models exist, logistic regression was chosen for three reasons:

1. **Explainability** — every decision can be decomposed and explained to regulators, in stark contrast to the black-box nature of neural networks.
2. **Scorecard transferability** — results convert cleanly into a FICO-style points-based system.
3. **Stability** — logistic regression is robust under post-facto changes such as new data standards or the availability of new features.

To prepare features, we use **Weight of Evidence (WoE)** encoding and capture the **Information Value (IV)** for each feature. This is the industry standard for IRB models because it handles all data on a consistent scale, captures non-linear relationships through binning, and produces a fully transparent model — every feature's contribution to the final score can be traced and explained.

### Results

| Metric | Value | Regulatory Benchmark |
|---|---|---|
| Gini Coefficient | 0.47 | > 0.30 |
| KS Statistic | 0.35 | > 0.20 |
| PSI | 0.0003 | < 0.10 |

All metrics fall within regulatory benchmarks for retail credit models.

### Limitations & Further Work

While a fruitful exercise in learning how banks model credit risk, this model is academic in nature. Deployment in a production banking environment would require three years of internal validation before receiving approval for regulatory capital purposes from OSFI. Further exploration might involve:

- Modelling all three components: PD, EAD, and LGD
- Adding a machine learning framework for comparison (XGBoost would be a good starting point)

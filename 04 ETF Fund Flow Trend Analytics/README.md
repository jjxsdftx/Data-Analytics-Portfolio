# ETF Fund Flow Trend Analytics

**A decision-oriented analytics project that turns fund-flow data into investor-behaviour signals, peer-relative trend indicators, and product strategy recommendations.**

[Review the ETF modelling notebook](./Code%20Hongquan%20Fan%20A1913143.ipynb) ·
[Review the FTI implementation](./Hongquan%20Fan%20A1913143.ipynb) ·
[Read the FTI design specification](./Optimal_FTI_Design.md)

## Project at a Glance

- **386,955 monthly US ETF observations**, covering **5,688 unique ETFs** and **300 months** from 2000 to 2024
- **182,558 Australian fund-flow records**, covering **782 ETFs** and **2,999 managed funds** from January 2022 to July 2025
- An end-to-end workflow spanning data quality control, feature engineering, OLS and logistic modelling, K-means segmentation, sensitivity analysis, and business interpretation
- A custom **Fund Trend Indicator (FTI)** for peer-relative monitoring over 1-, 3-, 6-, and 12-month horizons, including de-seasonalised variants
- Honest out-of-sample validation that identifies where simple models add value—and where they do not

---

## 01 — Executive Summary

Fund managers need to understand whether investors are responding to recent performance, which products exhibit performance-chasing behaviour, and whether a fund's flow momentum is strong relative to comparable peers. This project combines long-horizon US ETF modelling with a peer-relative Fund Trend Indicator for Australian ETFs and managed funds. The analysis finds that recent returns alone have limited ability to predict future ETF flows, while statistically significant performance-chasing is concentrated in particular categories and smaller products. It also shows that average ETF market sensitivity has declined materially over time as the product ecosystem has diversified, supporting more targeted product positioning, marketing, and monitoring decisions.

## 02 — Business Problem

Raw fund flows are difficult to interpret:

- A large fund can attract more dollars than a small fund while still underperforming relative to its own scale.
- A positive flow observation does not reveal whether investors are responding to fund-specific performance, the overall market, seasonality, or persistent flows.
- Products with different investor bases can exhibit very different flow-performance relationships.
- Monthly flow signals are volatile, making a single-period measure unsuitable for strategic monitoring.
- ETF and managed-fund flows should not be compared without controlling for product type, peer group, fund size, and time horizon.

This project addresses four decision questions:

1. **Predictability:** Can historical fund and market returns predict the direction or magnitude of future ETF flows?
2. **Segmentation:** Which ETF groups exhibit performance-chasing, contrarian, or low-sensitivity behaviour?
3. **Product evolution:** How has ETF sensitivity to the broad market changed as the industry has matured?
4. **Monitoring:** How can a fund manager identify whether an individual fund's current flow trend is strong or weak relative to relevant peers?

The intended users are fund managers, product strategists, distribution teams, and investment analytics teams.

## 03 — Key Business Insights

### Insight 1: Recent returns are a weak standalone predictor of future ETF flows

The modelling workflow evaluates 20 representative ETFs using chronological 80/20 train-test splits. Adding calendar-month dummy variables improved RMSE for only **2 of 20 ETFs**; average test RMSE increased by **17.35%**, and average out-of-sample \(R^2\) declined by **0.403**. This indicates that generic seasonal variables mostly added complexity and overfitting rather than stable predictive signal.

Direction classification was more useful than magnitude prediction, but still limited:

- OLS directional accuracy: **40.9%** without month effects and **44.3%** with month effects
- Logistic-regression accuracy: **64.8%** without month effects and **62.6%** with month effects
- Logistic-regression AUC: **0.575** without month effects and **0.558** with month effects

**Why it matters:** recent returns should not be used as a standalone flow forecast. Market context, fees, distribution activity, news, liquidity, and investor composition are likely necessary for a production model. The negative result is commercially useful because it prevents false confidence and discourages over-engineered seasonal forecasts.

The notebook contains model-comparison charts, ETF-level holdout metrics, and category-level seasonality results:
[open the validation section](./Code%20Hongquan%20Fan%20A1913143.ipynb).

### Insight 2: Performance-chasing exists, but it is concentrated and segment-specific

Flow-performance beta measures how strongly current fund inflows respond to prior ETF returns. Of **5,688 ETFs**, the project obtained valid beta estimates for **3,659** products with sufficient history. Only **632 estimates (17.3%)** were statistically significant at the 5% level, but **568 of those were positive**, compared with 64 negative estimates.

The segmentation and statistical tests show that:

- Most ETFs have flow-performance beta close to zero.
- Strong positive or contrarian behaviour is concentrated in a smaller set of products.
- Extreme flow sensitivity is more common among small and medium-sized ETFs.
- Currency/Crypto ETFs show the strongest performance-chasing tendency.
- International/Emerging Market ETFs rank next, while Bond/Fixed Income ETFs show the weakest—and sometimes contrarian—relationship.
- Differences across fund categories are statistically significant.

**Why it matters:** investor behaviour is not uniform. High-beta products can benefit from timely performance communication and campaign activation, while low-beta products should be positioned around long-term allocation, cost, diversification, and portfolio role.

The notebook includes beta distributions, K-means maps, cluster profiles, representative products, and category tests:
[open the segmentation analysis](./Code%20Hongquan%20Fan%20A1913143.ipynb).

### Insight 3: ETF market sensitivity has declined as the product ecosystem has diversified

The market-beta analysis uses **272,046 observations across 3,711 ETFs** from December 2001 to December 2024. Average ETF market beta shows a statistically significant downward trend, with an annualised slope of **-0.0231** and a trend p-value below 0.001.

By December 2024, estimated average market beta was below one under every robust summary:

- **0.739** using the winsorised equal-weight mean
- **0.865** using AUM weighting
- **0.805** using the cross-sectional median
- **0.755** using a 5%–95% trimmed mean

The newest launch cohort had an average beta of **0.770**, compared with **0.855** for ETFs launched by 2007. This does not imply that ETFs have become disconnected from the market. Instead, it reflects the expansion from broad equity-index products into bonds, commodities, sectors, themes, and defensive strategies; AUM-weighted beta remains higher because large core products are still closely tied to the broad market.

**Why it matters:** product positioning should explicitly state whether an ETF is intended to deliver core market exposure, tactical performance, diversification, or defensive characteristics. A single market narrative is no longer appropriate for a highly differentiated ETF universe.

The notebook includes the long-run beta series, alternative estimators, confidence bands, and launch-cohort comparisons:
[open the market-sensitivity analysis](./Code%20Hongquan%20Fan%20A1913143.ipynb).

## 04 — Business Recommendations

### 1. Segment distribution strategy by flow-performance sensitivity

- Prioritise timely performance-led communications for high positive-beta products.
- Add risk warnings and suitability controls where performance-chasing may encourage short-term investor behaviour.
- Position low-beta products around fees, portfolio construction, diversification, and long-term holding objectives.
- Use category-specific benchmarks rather than a single market-wide campaign rule.

### 2. Use FTI as a monitoring and prioritisation tool—not a guaranteed forecast

- Track 1-month FTI for rapid changes in investor interest.
- Use 3- and 6-month FTI for campaign timing and distribution monitoring.
- Use 12-month FTI for strategic product reviews and persistent-flow assessment.
- Compare raw and de-seasonalised FTI before escalating an apparent flow signal.
- Investigate funds that remain below their peer midpoint across several horizons.

### 3. Avoid generic seasonality assumptions

Calendar-month indicators degraded average holdout performance in this sample. Seasonality should therefore be applied selectively, supported by product-level evidence, and validated on future periods before deployment.

### 4. Expand any production forecast with decision-relevant external features

A production model should test fees, spreads, media attention, distribution campaigns, fund age, volatility regime, benchmark performance, issuer strength, and macroeconomic conditions. Regularisation and rolling-window validation should be used to control overfitting.

### 5. Clarify each product's portfolio role

The long-run decline in average market beta indicates a more diverse ETF ecosystem. Product communication should clearly distinguish core beta exposure from tactical, thematic, income, commodity, and defensive use cases.

## 05 — Business Impact & Validation

### What has been validated

This repository validates analytical findings rather than claiming realised commercial impact:

- Chronological train-test splits preserve time ordering and reduce look-ahead bias.
- OLS models are evaluated against a historical-mean benchmark using RMSE, MAE, out-of-sample \(R^2\), and directional accuracy.
- Logistic models are assessed with accuracy, precision, recall, F1, and ROC-AUC.
- HC3 robust standard errors are used where heteroskedasticity is a concern.
- Extreme flow rates and cross-sectional beta estimates are winsorised to reduce outlier dominance.
- K-means solutions are assessed over multiple values of \(k\) using inertia and silhouette scores, with final cluster selection balancing statistical separation and business interpretability.
- Category differences are tested using both ANOVA and the non-parametric Kruskal-Wallis test.
- Market-beta conclusions are checked using equal-weighted, AUM-weighted, median, trimmed, and winsorised estimates.
- FTI is calculated separately for ETFs and managed funds and benchmarked within peer groups.

### Illustrative decision value

The FTI implementation demonstrates how the same fund can produce different signals across horizons. For the ATLAS Infrastructure Australian Feeder Fund – Hedged in December 2024:

- Raw FTI values were **0.591 (1M)**, **0.463 (3M)**, **0.798 (6M)**, and **0.633 (12M)**.
- The fund was above the peer midpoint over 1, 6, and 12 months, but below it over 3 months.
- After seasonal adjustment, the corresponding values were **0.419**, **0.621**, **0.848**, and **0.709**, materially changing the short-horizon interpretation.

This demonstrates why decision-makers should not rely on a single monthly flow number.

### Proposed production KPIs

If operationalised, the framework should be monitored using:

- Net-flow uplift versus a matched control group
- Campaign conversion and cost per acquired investor
- AUM retention after performance-led inflow periods
- Forecast AUC, calibration, and benchmark-relative RMSE
- FTI stability and rank turnover by horizon
- Model drift by category, size, and market regime
- False-alert rate for funds flagged as unusually strong or weak

No live campaign, realised revenue, or investment-return uplift is claimed in this repository.

## 06 — Analytical Approach

### Data coverage

The project uses two complementary analytical datasets:

**US ETF panel**

- 386,955 monthly observations
- 5,688 unique ETF identifiers
- January 2000 to December 2024
- ETF price, return, volume, shares outstanding, and identifier fields
- 300 monthly S&P 500 return observations

**Australian fund-flow panel**

- 26,890 ETF transaction records across 782 funds
- 155,668 managed-fund transaction records across 2,999 funds
- January 2022 to July 2025
- Inflow, outflow, net flow, transaction type, provider, and fund category
- 10 ETF categories and 11 managed-fund categories

### Data quality and feature engineering

The workflow:

1. Standardises dates and validates fund-month uniqueness.
2. Handles CRSP-specific negative price conventions using absolute quoted price.
3. Converts non-numeric return codes to missing values while retaining an audit trail.
4. Constructs AUM as price multiplied by shares outstanding.
5. Constructs fund flow from the change in shares outstanding multiplied by current price.
6. Normalises flow by lagged AUM to improve comparability across fund sizes.
7. Creates lagged fund returns, market returns, AUM, volume, and prior flow.
8. Applies logarithmic transformations to skewed size and volume variables.
9. Winsorises extreme flow rates before modelling.
10. Aggregates transaction-level Australian data into continuous fund-month panels.

### Predictive and explanatory modelling

- Nested OLS specifications isolate the incremental role of fund returns, market returns, size, liquidity, prior flows, and seasonality.
- Chronological holdouts test genuine forward performance.
- Logistic regression predicts positive versus negative flow direction.
- Flow-performance beta estimates investor response to prior performance at the individual-ETF level.
- K-means clustering combines behavioural sensitivity with AUM and market beta to build interpretable product segments.
- Rolling market-beta analysis tracks industry evolution through time and across launch cohorts.

### Fund Trend Indicator

The implemented FTI notebook produces peer-relative percentile scores from 0 to 1:

\[
FTI_{i,t}^{(h)} = \frac{N_{c,t}^{(h)} - Rank_{i,c,t}^{(h)}}{N_{c,t}^{(h)} - 1}
\]

where \(h\) is the 1-, 3-, 6-, or 12-month horizon and ranking occurs within the same fund category and month. A value above 0.5 indicates above-median flow growth relative to peers.

The implementation also:

- Builds complete monthly fund panels
- Produces separate ETF and managed-fund measures
- Calculates 1M, 3M, 6M, and 12M indicators
- Estimates seasonal components using category-by-calendar-month medians
- Generates de-seasonalised FTI variants
- Visualises selected funds against peer averages

The accompanying [design specification](./Optimal_FTI_Design.md) proposes a next-generation extension: monthly within-category z-scores aggregated with linearly decaying weights. This design places more emphasis on recent observations while preserving comparability across months. It is documented separately as a transparent methodology roadmap rather than presented as the currently implemented percentile engine.

## Technology

- Python
- pandas and NumPy
- Matplotlib and Seaborn
- statsmodels
- scikit-learn
- SciPy
- Jupyter Notebook

## Repository Guide

### [`Code Hongquan Fan A1913143.ipynb`](./Code%20Hongquan%20Fan%20A1913143.ipynb)

The primary US ETF analytics notebook. It covers:

- Data audit and CRSP-specific cleaning
- AUM and fund-flow construction
- Flow-performance OLS models
- Chronological out-of-sample evaluation
- Logistic flow-direction classification
- ETF behavioural segmentation with K-means
- Cross-category statistical testing
- Long-run market-beta and launch-cohort analysis
- Business interpretation

### [`Hongquan Fan A1913143.ipynb`](./Hongquan%20Fan%20A1913143.ipynb)

The Australian ETF and managed-fund FTI implementation. It covers:

- Exploratory analysis of 182,558 transaction records
- Fund-month panel construction
- Peer-relative multi-horizon FTI
- De-seasonalisation
- ETF and managed-fund peer comparisons
- Worked sample calculations and visual dashboards

### [`Optimal_FTI_Design.md`](./Optimal_FTI_Design.md)

The mathematical design document for the weighted z-score extension, including:

- Size normalisation
- Within-category benchmarking
- Linear-decay weighting
- Multi-horizon construction
- Missing-value rules
- De-seasonalised variants

## Suggested Review Path

For a quick review:

1. Read the Executive Summary and Key Business Insights in this README.
2. Open the US ETF notebook and review sections 5–8 for validation, segmentation, market-beta analysis, and business interpretation.
3. Open the FTI notebook and review sections 4–6 for indicator construction, peer comparisons, and worked examples.
4. Read the design specification for the proposed weighted z-score extension.

## Data Access and Reproducibility

The raw datasets are intentionally not included because they originate from licensed or course-provided sources and may not be redistributed publicly. The committed notebooks retain executed outputs, charts, model summaries, and sample calculations so the complete analysis can be reviewed without access to the underlying data.

To reproduce the analysis, authorised users should place the source files in a local `dataset/` directory using the filenames referenced by the notebooks. Required Python packages include:

```text
pandas
numpy
matplotlib
seaborn
scipy
statsmodels
scikit-learn
openpyxl
```

## Limitations and Next Steps

- Flow models use historical observational data and do not establish causal effects.
- The 20-ETF predictive comparison is designed for interpretable diagnostics, not universal performance claims.
- Transaction coverage and product availability vary across funds and periods.
- Zero-filling inactive months is useful for a continuous panel but may represent either genuine inactivity or missing reporting.
- The percentile FTI and weighted z-score design are complementary specifications; the latter remains a documented extension.
- A production system should add external commercial features, rolling retraining, automated data-quality checks, and model-drift monitoring.
- Future work should test whether FTI predicts subsequent AUM retention, redemptions, or campaign response.
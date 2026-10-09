# Hongquan Fan | Data Analytics Portfolio

Decision-oriented analytics projects across financial services, risk, customer analytics, public policy, retail, investment, and entertainment.

Across 10 projects, this portfolio demonstrates how I turn raw data into business decisions using **Python, statistical modelling, machine learning, optimisation, Excel, Tableau, Streamlit, and AI-assisted analytics**. Each project is documented around the business problem, analytical approach, validated result, recommendation, and limitation—not just the code.

## Start Here

| Featured project | What it demonstrates | Key evidence | Review |
|---|---|---|---|
| **Consumer Credit Scoring & Capital Allocation** | End-to-end risk analytics: multi-table feature engineering, an interpretable scorecard, probability calibration, and linear-programming allocation | 307,511 applications; validation AUC **0.7628** and KS **0.3994**; scenario-based expected profit **14.804B** | [Project overview](<01 Consumer Credit Scoring & Capital Allocation/README.md>) · [Scorecard](<01 Consumer Credit Scoring & Capital Allocation/02_Logistic_Regression_Scorecard_Fast_Reproduction.ipynb>) · [Optimisation](<01 Consumer Credit Scoring & Capital Allocation/03_Prescriptive_Analytics_LP.ipynb>) |
| **ETF Fund Flow Trend Analytics** | Financial analytics, honest out-of-sample testing, investor-behaviour segmentation, and custom indicator design | 5,688 US ETFs plus 3,781 Australian funds; OLS/logistic models, K-means, market-beta analysis, and multi-horizon FTI | [Project overview](<04 ETF Fund Flow Trend Analytics/README.md>) · [US ETF analysis](<04 ETF Fund Flow Trend Analytics/Code Hongquan Fan A1913143.ipynb>) · [FTI design](<04 ETF Fund Flow Trend Analytics/Optimal_FTI_Design.md>) |
| **Fatal Traffic Accident Risk Analytics** | Statistical inference and temporal machine learning translated into public-sector intervention priorities | 358,460 fatal crashes; 2023–2024 holdout AUC **0.818** and recall **0.768** | [Project overview](<05 Fatal Traffic Accident Risk Analytics/README.md>) · [Notebook](<05 Fatal Traffic Accident Risk Analytics/Python code.ipynb>) |
| **Korea COVID-19 AI Dashboard** | Interactive data storytelling, modular dashboard design, and context-aware AI summaries | Five analytical modules covering outbreak, geography, transmission, patients, and policy/environmental impact | [Project overview](<06 South Korea COVID-19 AI-Powered Data Visualization Dashboard/README.md>) · [Live Streamlit app](https://korea-covid19-dashboard-shfhrdk5jvg2mqx92bvgf8.streamlit.app/) · [Static showcase](https://korea-covid19-intelligence.peachylily8.chatgpt.site) |

## Complete Project Directory

### Python, Statistics, Machine Learning & Optimisation

| # | Project | Business decision supported | Methods and tools | Result |
|---:|---|---|---|---|
| 01 | [Consumer Credit Scoring & Capital Allocation](<01 Consumer Credit Scoring & Capital Allocation/README.md>) | Which applicants should receive credit, and how should lending capital be allocated under a loss constraint? | Python, multi-table feature engineering, WOE, logistic regression, scorecard mapping, calibration, linear programming | Validation AUC **0.7628**; KS **0.3994**; risk capacity identified as the binding portfolio constraint |
| 02 | [SLG High-Value Player Prediction](<02 High-Value Player Prediction/README.md>) | Which players should receive differentiated offers and retention treatment on day 7? | EDA, behavioural feature engineering, logistic classification, gradient-boosting regression, two-stage modelling | 2.29M players; payer AUC **0.971**; RMSE improved from **62.00** to **54.42** |
| 03 | [E-commerce Order Anomaly Detection](<03 E-commerce Order Anomaly Detection/README.md>) | Which orders should be released, reviewed, or intercepted? | Data-quality investigation, business-risk features, Random Forest, GBDT, XGBoost, weighted soft voting | 131,282 cleaned rows; unseen-order holdout AUC **0.8150** |
| 04 | [ETF Fund Flow Trend Analytics](<04 ETF Fund Flow Trend Analytics/README.md>) | How should fund managers interpret performance-chasing, market sensitivity, and peer-relative flow momentum? | OLS, logistic regression, chronological validation, robust inference, K-means, custom multi-horizon FTI | Recent returns shown to be weak standalone predictors; significant performance-chasing concentrated in specific segments |
| 05 | [Fatal Traffic Accident Risk Analytics](<05 Fatal Traffic Accident Risk Analytics/README.md>) | Where and when should road-safety agencies deploy limited intervention resources? | FARS data integration, fixed-effects logistic inference, time-based validation, four-model comparison | Holdout AUC **0.818**; approximately **77%** of high-risk state-months detected |
| 06 | [Korea COVID-19 AI Dashboard](<06 South Korea COVID-19 AI-Powered Data Visualization Dashboard/README.md>) | How can public-health users explore outbreak patterns and receive evidence-grounded explanations? | Python, Streamlit, Plotly, Pandas, NetworkX, OpenAI API | Interactive drill-down design with AI summaries built from filtered aggregate statistics rather than raw CSV data |

### Excel Analytics

| # | Project | Business decision supported | Methods and tools | Result |
|---:|---|---|---|---|
| E01 | [Retail Operations Analysis](<Data Analysis with Excel/01 Retail Operations Analysis/README.md>) | How should a retailer respond to declining sales, stock mismatch, staffing gaps, and weakening VIP contribution? | Excel formulas, year-over-year KPIs, SKU/size analysis, inventory-turnover benchmarking | Identified a **52.0%** sales decline and only **8 of 20** best-selling SKUs with adequate size availability · [Workbook](<Data Analysis with Excel/01 Retail Operations Analysis/Retail Operations Analysis.xlsx>) |
| E02 | [RFM Customer Value Analysis](<Data Analysis with Excel/02 RFM Customer Value Analysis/README.md>) | Which customers should be retained, nurtured, or reactivated? | Excel aggregation, RFM scoring, rule-based segmentation, PivotTables, PivotCharts | Segmented 3,403 customers; Lost and New groups represent **84.4%** of the customer base · [Workbook](<Data Analysis with Excel/02 RFM Customer Value Analysis/RFM Customer Value Analysis.xlsx>) |

### Tableau & Visual Analytics

| # | Project | Business decision supported | Methods and tools | Result |
|---:|---|---|---|---|
| T01 | [Venture Capital Investment Strategy Analysis](<Data Analysis with Tableau/01 Investment Strategy Analysis of Top 100 Venture Capital Firms/README.md>) | Where should an investment team focus sourcing, benchmarking, and due diligence? | Tableau, geographic and time-series analysis, LOD calculations, clustering, interactive filtering | 1,974 deals; top three named institutions account for **85.7%** of recorded capital · [Interactive dashboard](https://public.tableau.com/views/01InvestmentStrategyAnalysisofTop100VentureCapitalFirms/sheet0?:language=zh-CN&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) · [Workbook](<Data Analysis with Tableau/01 Investment Strategy Analysis of Top 100 Venture Capital Firms/01 Investment Strategy Analysis of Top 100 Venture Capital Firms.twbx>) |
| T02 | [Data-Driven Analysis for Successful Film Production](<Data Analysis with Tableau/02 Data-Driven Analysis for Successful Film Production/README.md>) | How should a film team assess genre, budget, talent, story, and runtime before greenlighting a project? | Tableau, data modelling, LOD calculations, clustering, interactive visual storytelling | 4,937 films; top 10% generate **42.5%** of recorded box office · [Interactive dashboard](https://public.tableau.com/views/02Data-DrivenAnalysisforSuccessfulFilmProduction/1_4?:language=zh-CN&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) · [Workbook](<Data Analysis with Tableau/02 Data-Driven Analysis for Successful Film Production/02 Data-Driven Analysis for Successful Film Production.twbx>) |

## Capability Map

| Capability | Evidence in this portfolio |
|---|---|
| **Data preparation and quality control** | Multi-table joins, cross-year schema harmonisation, duplicate and anomaly investigation, missing-value strategy, panel construction |
| **Exploratory and business analysis** | Customer behaviour, fraud risk, product availability, fund flows, public health, road safety, investment, and film-market analysis |
| **Statistical modelling** | Logistic regression, OLS, robust standard errors, odds ratios, hypothesis testing, calibration, and sensitivity analysis |
| **Machine learning** | Random Forest, Gradient Boosting, XGBoost, ensemble learning, K-means clustering, imbalanced classification, and two-stage models |
| **Validation discipline** | Stratified and chronological holdouts, Order-ID group splits, cross-validation, benchmark comparisons, AUC, KS, RMSE, recall, precision, and drift considerations |
| **Prescriptive analytics** | Risk-constrained capital allocation, linear programming, shadow-price interpretation, and scenario testing |
| **Business intelligence** | Excel operational dashboards, RFM segmentation, Tableau stories, Streamlit applications, and interactive Plotly visualisation |
| **Decision communication** | Executive summaries, quantified insights, prioritised recommendations, implementation guardrails, KPI plans, and explicit limitations |

## Suggested Review Paths

- **Five-minute portfolio scan:** review the four projects in [Start Here](#start-here).
- **Risk and machine learning:** [Credit Scoring](<01 Consumer Credit Scoring & Capital Allocation/README.md>) → [Order Anomaly Detection](<03 E-commerce Order Anomaly Detection/README.md>) → [Traffic Risk](<05 Fatal Traffic Accident Risk Analytics/README.md>).
- **Financial analytics:** [Credit & Capital Allocation](<01 Consumer Credit Scoring & Capital Allocation/README.md>) → [ETF Fund Flow Analytics](<04 ETF Fund Flow Trend Analytics/README.md>) → [Venture Capital Dashboard](<Data Analysis with Tableau/01 Investment Strategy Analysis of Top 100 Venture Capital Firms/README.md>).
- **Customer and commercial analytics:** [Game Spending Prediction](<02 High-Value Player Prediction/README.md>) → [RFM Segmentation](<Data Analysis with Excel/02 RFM Customer Value Analysis/README.md>) → [Retail Operations](<Data Analysis with Excel/01 Retail Operations Analysis/README.md>).
- **Dashboards and storytelling:** [COVID-19 AI Dashboard](<06 South Korea COVID-19 AI-Powered Data Visualization Dashboard/README.md>) → [Film Production Dashboard](<Data Analysis with Tableau/02 Data-Driven Analysis for Successful Film Production/README.md>).

## Repository Guide

```text
Data-Analytics-Portfolio/
├── 01 Consumer Credit Scoring & Capital Allocation/
├── 02 High-Value Player Prediction/
├── 03 E-commerce Order Anomaly Detection/
├── 04 ETF Fund Flow Trend Analytics/
├── 05 Fatal Traffic Accident Risk Analytics/
├── 06 South Korea COVID-19 AI-Powered Data Visualization Dashboard/
├── Data Analysis with Excel/
│   ├── 01 Retail Operations Analysis/
│   └── 02 RFM Customer Value Analysis/
└── Data Analysis with Tableau/
    ├── 01 Investment Strategy Analysis of Top 100 Venture Capital Firms/
    └── 02 Data-Driven Analysis for Successful Film Production/
```

Each project README follows a consistent review structure:

1. Executive summary
2. Business problem
3. Key insights
4. Recommendations
5. Business impact and validation
6. Analytical approach

## Reproducibility & Scope

- Executed Jupyter notebooks retain analytical outputs and visualisations for review.
- Excel and packaged Tableau workbooks are included where redistribution is permitted.
- Some raw datasets are excluded because of file size, licensing, or course-provided access restrictions; project READMEs document the required sources and reproduction steps.
- Reported results are offline analytical findings or scenario estimates unless a project explicitly states otherwise. Limitations and production-validation plans are documented within each project.

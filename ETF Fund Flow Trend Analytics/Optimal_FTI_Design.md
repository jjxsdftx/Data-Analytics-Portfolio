# Optimal FTI Design — Fund Trend Indicator

## Overview

This document presents the optimal Fund Trend Indicator (FTI) design, synthesised from four candidate approaches. The design satisfies all assignment requirements: size comparability between large and small funds, within-category benchmarking, multi-horizon trend capture, de-seasonalised variants, and separate treatment of ETFs vs Managed Funds.

---

## Design Rationale

The final design draws on the strengths of four candidate approaches:

| Source | Contribution |
|--------|-------------|
| **Design A** (growth-rate percentile ranking) | Uses **net flow growth rate** to normalise for fund size — directly addresses the requirement to make large and small funds comparable |
| **Design B** (weighted z-score sum) | Introduces **weighted aggregation** across months — recent months contribute more, capturing the "trend" concept |
| **Design C** (z-score + rolling average) | Provides the **cleanest formula framework** with standard statistical notation and natural multi-horizon extension |
| **Design D** (z-score then percentile) | The **percentile output** (0–100) is the most intuitive presentation for fund managers (used optionally in visualisations) |

---

## Formulas

### Step 1 — Size Normalisation: Net Flow Growth Rate

$$GrowthRate_{i,c,t} = \frac{NetFlow_{i,c,t} - NetFlow_{i,c,t-1}}{|NetFlow_{i,c,t-1}|}$$

| Symbol            | Definition                                            |
| ----------------- | ----------------------------------------------------- |
| $i$               | Individual fund                                       |
| $c$               | Fund category (`fund_type`)                           |
| $t$               | Month                                                 |
| $NetFlow_{i,c,t}$ | Net flow of fund $i$ in category $c$ during month $t$ |

- The denominator uses the **absolute value** of the previous month's net flow to avoid sign reversal when the base flow is negative.
- If the denominator is zero or missing, the growth rate is set to **missing**.

**Why growth rate?** The assignment explicitly requires making fund flows "comparable for large vs small funds." Growth rate normalises absolute flow changes to a common scale regardless of fund size.

---

### Step 2 — 1-Month FTI (Main Measure)

$$z_{i,c,t} = FTI_{(i,c,t)} = \frac{GrowthRate_{i,c,t} - \mu_{c,t}}{\sigma_{c,t}}$$

| Symbol | Definition |
|--------|-----------|
| $z_{i,c,t}$ | Monthly z-score of fund $i$ (reused in Step 3) |
| $\mu_{c,t}$ | Mean of $GrowthRate$ across all funds in category $c$ at month $t$ |
| $\sigma_{c,t}$ | Standard deviation of $GrowthRate$ across all funds in category $c$ at month $t$ |

**Interpretation:**

- $FTI > 0$: the fund's flow growth **outperforms** the category average
- $FTI < 0$: the fund's flow growth **underperforms** the category average
- $FTI \approx 1$: approximately one standard deviation above the category mean
- $FTI \approx 2$: approximately two standard deviations above (very strong)

**Why apply z-score on top of growth rate?**

1. It makes FTI values **comparable across different months** (each month's distribution is standardised), which is essential for meaningful time-series visualisation.
2. It provides the **mathematical foundation** for the weighted multi-horizon aggregation in Step 3 — since each monthly z-score is on the same scale (mean 0, std 1), they can be meaningfully combined with weights.

---

### Step 3 — Multi-Horizon FTI (3M, 6M, 12M): Weighted Z-Score Aggregation

This step reuses the monthly z-scores from Step 2 and applies **linearly decaying weights** so that more recent months contribute more to the trend indicator.

$$FTI_{(i,c,t)}^{(h)} = \sum_{k=0}^{h-1} w_k \cdot z_{i,c,t-k}$$

where the weights follow a **linear decay**:

$$w_k = \frac{h - k}{h(h+1)/2}$$

ensuring $\sum_{k=0}^{h-1} w_k = 1$ and $w_0 > w_1 > \cdots > w_{h-1}$.

| Symbol | Definition |
|--------|-----------|
| $h$ | Horizon window: 3, 6, or 12 months |
| $z_{i,c,t-k}$ | Monthly z-score of fund $i$ from Step 2 at month $t-k$ |
| $w_k$ | Weight for the month that is $k$ periods ago |

**Weight examples:**

For **h = 3** (3-month FTI):

| $k$ | Meaning | $w_k$ |
|-----|---------|--------|
| 0 | Current month | 3/6 = 0.500 |
| 1 | 1 month ago | 2/6 = 0.333 |
| 2 | 2 months ago | 1/6 = 0.167 |

For **h = 6** (6-month FTI):

| $k$ | $w_k$ |
|-----|--------|
| 0 | 6/21 ≈ 0.286 |
| 1 | 5/21 ≈ 0.238 |
| 2 | 4/21 ≈ 0.190 |
| 3 | 3/21 ≈ 0.143 |
| 4 | 2/21 ≈ 0.095 |
| 5 | 1/21 ≈ 0.048 |

For **h = 12** (12-month FTI): $w_k = (12-k)/78$.

**Why weighted aggregation instead of simple average?**

- A fund's **recent** flow performance is more indicative of its current trend than performance many months ago.
- The linear decay is intuitive: the most recent month gets the highest weight, and the weight decreases steadily into the past.
- Because each $z_{i,c,t-k}$ is already standardised (mean 0, std 1 within its month), weighted summation is mathematically meaningful — different months are on the same scale.

**Effect:** the multi-horizon FTI smooths out short-term noise while emphasising the recent direction of the fund's flow performance relative to peers.

---

### Step 4 — De-Seasonalised Variant

1. Compute the **seasonal component** of net flows (e.g., median net flow per calendar month within each fund type).
2. Subtract the seasonal component to obtain **de-seasonalised net flows**.
3. Repeat Steps 1–3 using de-seasonalised net flows instead of raw net flows.
4. Output: $FTI_{DS}$, $FTI_{DS}^{(3)}$, $FTI_{DS}^{(6)}$, $FTI_{DS}^{(12)}$.

---

## Design Principles

- **ETFs and Managed Funds are calculated separately** (as required by the assignment brief).
- All comparisons are **within the same fund category** (`fund_type`).
- **Missing value handling:** if a fund's growth rate is missing for a given month, its FTI is also missing. Mean and standard deviation are computed only over non-missing values. For multi-horizon FTI, if any of the $h$ monthly z-scores is missing, the weighted sum is set to missing.
- If the number of valid funds in a category-month is **≤ 1**, FTI is set to missing (standard deviation is undefined).

---

## Summary of Indicators

| Indicator | Horizon | Input | Description |
|-----------|---------|-------|-------------|
| $FTI$ | 1 month | Raw net flows | Main measure — monthly z-score of growth rate |
| $FTI^{(3)}$ | 3 months | Raw net flows | Short-term trend — weighted sum of recent 3 monthly z-scores |
| $FTI^{(6)}$ | 6 months | Raw net flows | Medium-term trend — weighted sum of recent 6 monthly z-scores |
| $FTI^{(12)}$ | 12 months | Raw net flows | Long-term trend — weighted sum of recent 12 monthly z-scores |
| $FTI_{DS}$ | 1 month | De-seasonalised net flows | Seasonal-adjusted main measure |
| $FTI_{DS}^{(3)}$ | 3 months | De-seasonalised net flows | Seasonal-adjusted short-term trend |
| $FTI_{DS}^{(6)}$ | 6 months | De-seasonalised net flows | Seasonal-adjusted medium-term trend |
| $FTI_{DS}^{(12)}$ | 12 months | De-seasonalised net flows | Seasonal-adjusted long-term trend |

---

## Why This Is the Optimal Design

1. **Size comparability** (from Design A): growth rate eliminates the bias toward large funds with higher absolute flows.
2. **Clean formula framework** (from Design C): z-score is a universally understood statistical method with clear notation.
3. **Weighted trend capture** (from Design B): linearly decaying weights ensure recent months matter more, faithfully reflecting the "trend" concept.
4. **No redundant steps** (avoiding Design D's flaw): the z-score step is mathematically necessary — it standardises each month so that weighted aggregation across months is meaningful.
5. **Pluggable de-seasonalisation**: simply swap the input net flows to obtain the seasonal-adjusted variant.
6. **Concise methodology**: the entire formula set fits within a 2-page Word document.

---

## Visualisation Notes

- Time-series line plots with **y = 0 reference line** (peer average) clearly show periods of outperformance vs underperformance.
- Optionally, a **percentile rank** (0–100) can be derived from FTI values for a more business-friendly presentation to fund managers.

# Corp. Favorita — Demand Forecasting & Operational Risk Management

**End-to-end ML pipeline** applied to a real-scale retail supply chain problem.

---

## Business Problem

Corp. Favorita operates 54 stores in Ecuador across 33 product families. Without predictive support, inventory decisions generate systematic operational risk:

| Error Type | Operational Consequence | Financial Impact |
|---|---|---|
| **Overstock** (predict > actual) | Perishable waste, tied-up capital | Shrinkage loss + storage cost |
| **Understock** (predict < actual) | Out-of-stock, customer loss | Lost sale + reputational risk |

This project frames demand forecasting as an **operational risk problem**, not just a regression exercise.

---

## Results Summary

| Metric | Value |
|---|---|
| Dataset | 3,029,400 records (2013–2017) |
| Final features | 92 (20 numeric + 72 One-Hot encoded) |
| R² — XGBoost | **0.9729** |
| MAE — XGBoost | 60.73 units |
| Error reduction vs. baseline | **63.9%** (MAE: 168.3 → 60.8 units) |
| Estimated annual savings | **~$35.0M USD** *(assumptions documented in Section 7)* |
| XGBoost training time | 0.3 minutes |

---

## Why This Matters for Operations Roles

This project demonstrates skills that translate directly to **Support Operations, Trust & Safety, and Data Operations** contexts:

- **Threshold-based alert system**: automated triage of 79.6% of predictions, manual review for 13.2%, escalation for 7.2% — directly analogous to ticket routing and escalation logic in support operations
- **Error pattern analysis**: identifying where and why a model fails, not just reporting aggregate metrics
- **Documented assumptions**: transparent methodology with explicit limitations — critical for audit-ready operations
- **Business impact quantification**: converting model performance into financial and operational terms for non-technical stakeholders

---

## Pipeline Structure

```
1. Business Context & Data Loading   → Operational risk framing, 3M+ record ingestion
2. Exploratory Data Analysis (EDA)   → Decision-oriented: seasonality, Pareto, promotions
3. Feature Engineering               → Temporal lags, calendar variables, null imputation
4. Modeling                          → Random Forest baseline vs XGBoost optimized
5. SHAP Explainability               → Global feature importance + individual prediction audit
6. Error Analysis                    → Bias detection, error thresholds, operational tiers
7. Business Impact                   → Savings quantification with transparent assumptions
8. Operational Recommendations       → Concrete, prioritized action items
```

---

## Key Technical Decisions

**Strict temporal split** — Training: Jan 2013 – May 2017 / Test: Jun–Aug 2017. No data leakage.

**Null imputation strategy**:
- `store_transactions` (9.05% null) → median by store (preserves traffic patterns)
- `dcoilwtico` lags (oil price gaps on weekends) → forward fill
- `sales` lags → rows dropped (no history available for first records)

**Model selection**: Random Forest and XGBoost performed near-identically (RF: R²=0.973, XGBoost: R²=0.973). XGBoost selected for production due to 25x faster training and better scalability.

**SHAP explainability**: Top predictors are `sales_avg_7`, `sales_lag_1`, and `onpromotion` — confirming that recent sales history and promotional context drive demand more than macroeconomic factors.

---

## Operational Alert System

Derived from error distribution analysis on 135,432 test predictions:

| Error Tier | Threshold | Volume | Action |
|---|---|---|---|
| ✅ Auto-approve | < 50 units | 79.6% | Accept prediction automatically |
| 🔍 Review | 50–200 units | 13.2% | Check for active promotion or special event |
| ⚠️ Manual review | 200–500 units | 4.6% | Human review before replenishment order |
| 🚨 Escalate | > 500 units | 2.6% | Escalate to operations supervisor |

---

## Tech Stack

```
Python 3.10 · Pandas · Scikit-learn · XGBoost 2.1.1 · SHAP 0.49.1
Matplotlib · Seaborn · Jupyter Notebook
```

---

## Limitations (documented for credibility)

1. **Short test window**: 76 days (Jun–Aug 2017). Does not include December — the highest-demand month.
2. **Unverified financial assumptions**: unit price ($2.50) and margin (25%) are sector benchmarks, not Corp. Favorita internal data.
3. **Single global model**: may underperform on niche families (AUTOMOTIVE, BOOKS). Per-family models recommended for top error categories.
4. **No additional macroeconomic variables**: inflation, unemployment, and exchange rate could improve predictions during economic disruption periods.

---

## Data Source

Public Kaggle competition: [Store Sales – Time Series Forecasting](https://www.kaggle.com/competitions/store-sales-time-series-forecasting). All analysis and conclusions are original work.

---

## Author

**Andrea Peralta**
Environmental Engineer · Executive Master in Business Analytics (EMIBA), ESADE Business School
[LinkedIn](https://www.linkedin.com/in/andrea-peralta-m-195531166/) · [GitHub](https://github.com/andrea-peralta)

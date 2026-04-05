# 🚀 Startup Health Scoring — Weighted Composite Scoring with Random Forest Validation

![Python](https://img.shields.io/badge/Python-3.8+-blue?style=flat-square&logo=python)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?style=flat-square&logo=jupyter)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-green?style=flat-square&logo=scikit-learn)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?style=flat-square&logo=pandas)
![Random Forest](https://img.shields.io/badge/Model-Random%20Forest%20Regressor-blueviolet?style=flat-square)

> A **weighted composite scoring model** built to evaluate and rank startup health across 100 startups — using Min-Max normalisation, business-intuition-driven feature weights, and **Random Forest Regression** for feature importance validation. The pipeline covers data preprocessing, normalisation, composite score computation, startup ranking, visualisation, and cross-validated model evaluation.

---

## 📌 Problem Statement

Evaluating startup health is inherently multi-dimensional — no single metric tells the full story. Investors and analysts need a unified, interpretable score that aggregates financial, traction, and team signals into a single comparable number. This project builds a scoring model that:

- **Ranks startups** on a 0–100 composite health score derived from six key business metrics
- **Handles directional feature differences** — positively and negatively correlated features are normalised and scored appropriately (higher burn rate = lower score)
- **Validates feature weights** using a Random Forest Regressor with cross-validated RMSE to confirm which features drive composite scores most strongly

---

## 🎯 Objectives

- Load and explore a 100-startup dataset covering team, market, traction, and financial metrics
- Apply **Min-Max normalisation** to all features, reversing the scale for negatively correlated features (burn rate)
- Compute a **weighted composite score** (out of 100) using business-intuition-driven weights
- **Rank all 100 startups** and extract the Top 10 and Bottom 10 performers
- Visualise score distribution, feature correlations, and feature importances
- Train a **Random Forest Regressor** on the composite scores and evaluate via **5-fold cross-validated RMSE**

---

## 📂 Dataset

| Property | Detail |
|---|---|
| File | `Startup_Scoring_Dataset.csv` |
| Rows | 100 startups |
| Columns | 7 (1 ID + 6 features) |
| Target | `composite_score` — computed weighted health score (0–100) |
| Score Range | ~19 (weakest) to ~82 (strongest) |

### Dataset Columns

| Column | Description | Scoring Direction |
|---|---|---|
| `startup_id` | Unique startup identifier (S001–S100) | — (ID only) |
| `team_experience` | Average years of experience of the founding team | ✅ Positive — higher is better |
| `market_size_million_usd` | Addressable market size in million USD | ✅ Positive — higher is better |
| `monthly_active_users` | Monthly active user count | ✅ Positive — higher is better |
| `monthly_burn_rate_inr` | Monthly cash burn in INR | ❌ Negative — lower is better (reversed) |
| `funds_raised_inr` | Total funds raised in INR | ✅ Positive — higher is better |
| `valuation_inr` | Current startup valuation in INR | ✅ Positive — higher is better |

### Dataset Statistics

| Feature | Mean | Min | Max |
|---|---|---|---|
| `team_experience` | 5.91 yrs | 1 | 10 |
| `market_size_million_usd` | $548.5M | $14M | $996M |
| `monthly_active_users` | 52,003 | 954 | 98,606 |
| `monthly_burn_rate_inr` | ₹52.7L | ₹1.66L | ₹99.3L |
| `funds_raised_inr` | ₹2.26Cr | ₹11.9K | ₹4.97Cr |
| `valuation_inr` | ₹23.2Cr | ₹1.01Cr | ₹49.8Cr |

---

## 🔬 Methodology

```
Startup_Scoring_Dataset.csv
       │
       ▼
Data Loading & Inspection
       │   ├── Load CSV with pandas
       │   ├── Inspect shape, dtypes, nulls
       │   └── Descriptive statistics (df.describe())
       │
       ▼
Data Preprocessing & Normalisation
       │   ├── Identify positive features:
       │   │       team_experience, market_size_million_usd, monthly_active_users,
       │   │       funds_raised_inr, valuation_inr
       │   ├── Identify negative features:
       │   │       monthly_burn_rate_inr
       │   ├── MinMaxScaler → scale positive features [0, 1]
       │   └── MinMaxScaler → scale negative features then reverse: 1 - scaled_value
       │
       ▼
Composite Score Computation
       │   ├── Feature weights (business-intuition based):
       │   │       monthly_active_users    → 0.25
       │   │       market_size_million_usd → 0.20
       │   │       team_experience         → 0.15
       │   │       funds_raised_inr        → 0.15
       │   │       valuation_inr           → 0.15
       │   │       monthly_burn_rate_inr   → 0.10
       │   └── composite_score = Σ(feature × weight) × 100
       │
       ▼
Ranking & Interpretation
       │   ├── Rank all 100 startups by composite_score (descending)
       │   ├── Extract Top 10 startups
       │   └── Extract Bottom 10 startups
       │
       ▼
Visualisations
       │   ├── Bar chart — Composite scores for all 100 startups (sorted)
       │   ├── Histogram + KDE — Score distribution
       │   └── Heatmap — Feature correlation matrix
       │
       ▼
Model Evaluation & Feature Importance
       ├── RandomForestRegressor on X (6 features) → y (composite_score)
       ├── 5-fold Cross-Validated RMSE
       └── Feature importances — bar chart (sorted)
```

---

## 📊 Results

### Composite Score Summary

| Metric | Value |
|---|---|
| Highest Score | ~82 |
| Lowest Score | ~19 |
| Score Distribution | Approximately normal, centred around 45–50 |

### Feature Weights (Scoring Formula)

| Feature | Weight | Rationale |
|---|---|---|
| `monthly_active_users` | 25% | Strongest traction signal |
| `market_size_million_usd` | 20% | Market opportunity size |
| `team_experience` | 15% | Execution capability |
| `funds_raised_inr` | 15% | Investor confidence |
| `valuation_inr` | 15% | Market-perceived value |
| `monthly_burn_rate_inr` | 10% | Efficiency / runway health |

### Random Forest Feature Importance

| Rank | Feature | Importance |
|---|---|---|
| 1 | `monthly_active_users` | ~0.41 |
| 2 | `funds_raised_inr` | ~0.25 |
| 3 | `market_size_million_usd` | ~0.11 |
| 4 | `valuation_inr` | ~0.09 |
| 5 | `team_experience` | ~0.09 |
| 6 | `monthly_burn_rate_inr` | ~0.05 |

> The Random Forest feature importances closely mirror the assigned business weights — validating that the chosen weighting scheme is consistent with data-driven patterns.

> 📝 *Refer to `startup_health_scoring.ipynb` for full rankings, classification outputs, cross-validated RMSE, feature importance chart, and all visualisations.*

---

## 📈 Visualisations

| Plot | Description |
|---|---|
| `bar_chart.png` | Sorted bar chart of composite scores across all 100 startups |
| `score_distribution.png` | Histogram + KDE showing score distribution centred ~45–50 |
| `correlation_heatmap.png` | Feature correlation heatmap — all features are largely uncorrelated (max ~0.15), confirming independence |
| `feature_importance.png` | Horizontal bar chart of Random Forest feature importances |

---

## 💡 Key Insights

- **Monthly active users** is overwhelmingly the most important feature (~41% importance), reinforcing traction as the primary health indicator for startups
- **Funds raised** is the second-strongest signal (~25%), reflecting investor-validated potential
- **Low inter-feature correlation** (all pairwise correlations below 0.16) confirms the six features contribute independent information — no redundancy in the scoring formula
- **Reversed burn rate normalisation** correctly penalises high-burn startups while rewarding capital efficiency, without requiring separate treatment in the model
- **Score distribution is approximately normal** (~19 to ~82), centred around 45–50 — indicating the scoring formula produces a well-spread, discriminative ranking rather than clustering startups at extremes
- **Random Forest importances validate the business weights** — the relative rankings from the model match the intuition-driven weight assignments, building confidence in the scoring formula

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.8+ | Core programming language |
| Pandas | Data loading, preprocessing, feature engineering |
| NumPy | Numerical operations and score computation |
| Scikit-learn | MinMaxScaler, RandomForestRegressor, cross_val_score, mean_squared_error |
| Matplotlib / Seaborn | Bar chart, score distribution histogram, correlation heatmap, feature importance plot |
| Jupyter Notebook | Interactive development and analysis |

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### Run the Notebook

```bash
# Clone the repository
git clone https://github.com/PJ2001-IND/Startup-Health-Scoring.git

# Navigate to the project directory
cd Startup-Health-Scoring

# Launch Jupyter Notebook
jupyter notebook startup_health_scoring.ipynb
```

> ⚠️ **Note:** Ensure `Startup_Scoring_Dataset.csv` is in the same directory as the notebook before running, as it is loaded with a relative path (`pd.read_csv('Startup_Scoring_Dataset.csv')`).

---

## 📁 Project Structure

```
📦 Startup-Health-Scoring
 ┣ 📓 startup_health_scoring.ipynb   # Full scoring pipeline
 ┣ 📄 Startup_Scoring_Dataset.csv    # 100-startup dataset (7 columns)
 ┣ 📊 bar_chart.png                  # Composite scores bar chart (sorted)
 ┣ 📊 score_distribution.png         # Score distribution histogram + KDE
 ┣ 📊 correlation_heatmap.png        # Feature correlation heatmap
 ┣ 📊 feature_importance.png         # Random Forest feature importances
 ┣ 📄 requirements.txt               # Python dependencies
 ┗ 📄 README.md                      # Project documentation
```

---

## ⚠️ Disclaimer

> This project is built purely for **educational and analytical purposes** as a data science case study. The composite scoring model and startup rankings are derived from a synthetic dataset and are intended to demonstrate weighted scoring, Min-Max normalisation, and Random Forest regression proficiency — not to serve as investment advisory or production startup evaluation tooling.

---

## 🔭 Future Scope

- Incorporate **sector/industry as a categorical feature** — different industries have different burn rate norms and growth expectations
- Apply **GridSearchCV** to tune `n_estimators`, `max_depth`, and `min_samples_leaf` for the Random Forest Regressor
- Experiment with **alternative weighting schemes** (e.g., PCA-derived weights, equal weights) and compare resulting rankings
- Add a **sensitivity analysis** — show how the composite score changes when individual feature weights are varied
- Build an **interactive Streamlit dashboard** where investors can adjust feature weights in real time and re-rank startups instantly
- Extend the dataset with **time-series features** (month-over-month MAU growth, burn rate trend) to capture startup trajectory, not just point-in-time health
- Explore **clustering (K-Means / DBSCAN)** to group startups into health tiers (e.g., High Growth, Stable, At Risk) beyond a simple rank

---

## 👤 Author

**Praasuk Jain**
- GitHub: [@PJ2001-IND](https://github.com/PJ2001-IND)
- LinkedIn: [praasuk-jain](https://www.linkedin.com/in/praasuk-jain-425b6b1a3/)

---

> ⭐ If you found this project useful, consider giving it a star!

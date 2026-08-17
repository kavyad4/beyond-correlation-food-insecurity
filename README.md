# Beyond Correlation: Environmental Predictors of Global Food Insecurity

This repository contains the research extension of **Sustaining Balance**, an applied project completed at Arizona State University in 2024. The original project focused on integrating food insecurity, environmental, socioeconomic, governance, and sentiment data. This research builds on that dataset and reformulates the problem as a **one-year-ahead forecasting task**.

The goal is to examine whether environmental, socioeconomic, governance, and sentiment indicators observed in year (t) can help predict severe food insecurity in year (t+1), and to compare conventional Pearson correlation with SHAP-based model interpretation.

## Research Questions

This work is guided by three questions:

1. To what extent can environmental, socioeconomic, governance, and sentiment indicators forecast severe food insecurity one year ahead?
2. Which predictors are most influential in the best-performing machine-learning model?
3. Do SHAP-based feature rankings lead to different conclusions from conventional Pearson correlation analysis?

## Relationship to the Applied Project

This study extends the earlier **Sustaining Balance** project rather than reproducing it.

The applied project developed the initial data collection and integration workflow using data from:

* Food and Agriculture Organization of the United Nations (FAO)
* World Bank development indicators
* environmental and agricultural indicators
* social media sentiment datasets

The research extension introduces several changes:

* revised country and year standardization
* corrected missing-value handling
* removal of non-country aggregates
* preservation of missing predictor values before model training
* separation of the food insecurity outcome from the predictor set
* one-year-ahead target construction
* temporal train-test evaluation
* training-only imputation and scaling
* country-clustered statistical inference
* Benjamini-Hochberg false discovery rate correction
* SHAP-based model interpretation
* LSTM comparison with classical machine-learning models

**Original Applied Project Repository:**
`[ADD LINK TO SUSTAINING BALANCE REPOSITORY]`

## Dataset

The research base dataset combines country-year observations from FAO, the World Bank, and sentiment sources.

| Dataset Characteristic                     |     Value |
| ------------------------------------------ | --------: |
| Base observations                          |     2,167 |
| Countries / territories                    |       217 |
| Year range                                 | 2013–2022 |
| Duplicate country-year records             |         0 |
| Reported food insecurity observations      |       813 |
| Exact numeric food insecurity observations |       610 |
| Censored `<100` observations               |       203 |
| Sentiment observations                     |        97 |

FAO values reported as `<100` are retained in the research base dataset as censored observations. They are not converted to an assumed numeric value for forecasting.

The raw source datasets are not redistributed in this repository. See [`data/README.md`](data/README.md) for source information and dataset-construction notes.

## Forecasting Design

The forecasting task uses indicators from one year to predict food insecurity in the following year:

**Predictors at year (t) → Food insecurity at year (t+1)**

Only consecutive country-year pairs with an available numeric next-year target are retained.

The resulting forecasting sample contains:

| Forecasting Characteristic  |     Value |
| --------------------------- | --------: |
| One-year-ahead observations |       610 |
| Forecast target years       | 2015–2021 |
| Training observations       |       405 |
| Training target years       | 2015–2019 |
| Test observations           |       205 |
| Test target years           | 2020–2021 |
| Numeric predictors          |        27 |

Current-year food insecurity, next-year target fields, and variables used only for target construction are excluded from the predictor set.

## Predictor Groups

The analysis uses individual indicators representing environmental, socioeconomic, governance, and sentiment conditions.

Examples include:

### Environmental

* CO₂ emissions per capita
* nitrous oxide emissions
* renewable energy consumption
* renewable electricity output
* water productivity
* freshwater withdrawals
* water stress
* fossil-fuel electricity production
* alternative and nuclear energy use

### Socioeconomic

* access to electricity
* rural electricity access
* agriculture value added
* GDP growth
* trade as a percentage of GDP

### Governance

* Political Stability and Absence of Violence/Terrorism
* CPIA environmental sustainability
* CPIA business regulatory environment

### Sentiment

* current sentiment score
* lag-1 sentiment
* lag-2 sentiment

Selected lagged variables are included to capture short-term historical information.

## Models

Five classical regression models are evaluated:

* Ridge Regression
* Random Forest
* Gradient Boosting
* XGBoost
* LightGBM

A Long Short-Term Memory (LSTM) network is also evaluated as a temporal sequence model.

Performance is measured using:

* Root Mean Squared Error (RMSE)
* Mean Absolute Error (MAE)
* R²

The food insecurity target is transformed using `log1p` before model fitting because the original population counts are strongly right-skewed.

## Missing-Value Handling

Missing predictor values are preserved during construction of the research base dataset.

For the classical models:

1. the temporal train-test split is created first;
2. median imputation is fitted using the training data only;
3. the fitted imputer is applied to the test set;
4. feature scaling is also fitted on the training data only.

This prevents information from the held-out period from influencing preprocessing.

## Statistical Analysis

Pearson correlation is used to examine the marginal linear association between each predictor and next-year food insecurity.

Because the dataset contains repeated annual observations for the same countries, statistical inference also uses:

* country-clustered robust standard errors
* Benjamini-Hochberg false discovery rate correction

Correlation is used for interpretation rather than feature selection.

## SHAP Analysis

SHAP is used to explain how the best-performing tree-based model uses the available predictors.

The analysis includes:

* mean absolute SHAP feature importance
* global feature ranking
* SHAP beeswarm visualization
* comparison between SHAP rankings and Pearson correlation rankings

Pearson correlation and SHAP answer different questions. Pearson measures a marginal linear relationship, while SHAP describes how a feature contributes within a fitted multivariable model.

Neither is interpreted as evidence of causation.

## Repository Structure

```text
beyond-correlation-food-insecurity/
│
├── README.md
│
├── notebooks/
│   └── beyond_correlation_analysis.ipynb
│
├── data/
│   ├── README.md
│   └── research_base_dataset.csv
│
├── results/
│   ├── model_results.csv
│   ├── correlations.csv
│   ├── shap_values.csv
│   └── figures/
│       ├── final_comparison.png
│       ├── predictions.png
│       ├── shap_importance.png
│       └── shap_beeswarm.png
│
├── paper/
│   └── README.md
│
└── requirements.txt
```

## Reproducing the Analysis

Install the required Python packages:

```bash
pip install -r requirements.txt
```

The main analysis is available in:

```text
notebooks/beyond_correlation_analysis.ipynb
```

The notebook covers:

1. loading and validating the research dataset;
2. target transformation;
3. one-year-ahead target construction;
4. lag feature generation;
5. temporal train-test splitting;
6. training-only preprocessing;
7. classical machine-learning models;
8. Pearson and cluster-robust correlation analysis;
9. SHAP interpretation;
10. LSTM forecasting;
11. model comparison and result export.

## Output Files

The main numerical outputs are stored under `results/`:

* `model_results.csv` — model performance metrics
* `correlations.csv` — Pearson correlations, clustered p-values, and FDR-adjusted q-values
* `shap_values.csv` — mean absolute SHAP importance values

Figures are stored under `results/figures/`.

## Research Paper

The accompanying manuscript is currently being revised to reflect the corrected dataset and final research results.

The final paper will be added to:

```text
paper/
```

once the manuscript is complete.

## Data Sources

The research builds on publicly available data from:

* Food and Agriculture Organization of the United Nations (FAO)
* World Bank World Development Indicators
* World Bank governance and sustainability indicators
* Climate Sentiment in Twitter dataset
* Social Media Sentiments Analysis dataset

See [`data/README.md`](data/README.md) for additional details.

## Author

**Kavya Dwivedi**

Research interests include machine learning, explainable AI, reliable AI systems, structured prediction, and applied data science.

## Acknowledgment

This research extends the *Sustaining Balance: Innovative Data Approaches for Food Security and Environment* applied project completed at Arizona State University in 2024.
# beyond-correlation-food-insecurity
Research extension on one-year-ahead food insecurity forecasting using ESG-related indicators, temporal validation, SHAP, and machine learning.

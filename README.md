# Aircraft Taxi-Out Time Prediction

A machine-learning study for predicting aircraft taxi-out times using operational flight data from the **PRC Data Challenge**. The project compares classical regression and ensemble models, evaluates them using **Combinatorial Purged Cross-Validation (CPCV)**, and examines their generalization under operational data shifts.

## Overview

Aircraft taxi-out time—the time an aircraft spends moving from its parking stand to the runway for takeoff—is influenced by airport congestion, aircraft characteristics, runway assignments, and temporal conditions. Accurate prediction can support improved departure planning and airport operations.

This project evaluates classical machine-learning approaches while emphasizing robust validation and generalization rather than relying solely on a single validation score.

## Models

### Final Models

* **Ridge Regression**
* **Lasso Regression**
* **Elastic Net**
* **XGBoost**
* **LightGBM**
* **CatBoost**
* **Stacking Ensemble**

Linear models provide baseline comparisons, while gradient-boosted tree models capture nonlinear relationships and interactions in operational data.

### Discontinued Experiment — Hidden Markov Model

A **Hidden Markov Model (HMM)** was also investigated as an alternative probabilistic approach for modeling taxi-out behavior as transitions between latent operational states.

The approach was ultimately **discontinued and excluded from the final model comparison** because its performance and practical suitability were not competitive with the supervised regression approaches used in the final pipeline.

The HMM experiment is retained in the repository for transparency and reproducibility of the research process.

## Methodology

### Data preprocessing and feature engineering

The target variable is `TAXITIME_SEC_mvt`, representing taxi-out time in seconds. Preprocessing and feature engineering include:

* Temporal features derived from flight timestamps
* Cyclical encoding of temporal variables
* Categorical operational features
* Exclusion of identifiers and target-leaking fields

### Model validation

Models are evaluated using **Combinatorial Purged Cross-Validation (CPCV)**.

| Configuration        |      Value |
| -------------------- | ---------: |
| Number of groups     |          5 |
| Test groups per fold |          2 |
| Total folds          |         10 |
| Purge window         | 60 minutes |
| Embargo window       | 30 minutes |

Purging and embargoing reduce the risk of temporal leakage between training and testing data.

### Evaluation metrics

* **RMSE** — primary metric
* **MAE** — average absolute prediction error
* **R²** — explained variance

## Results

Mean cross-validation performance across 10 CPCV folds:

| Model       |    MAE (s) |   RMSE (s) |         R² |
| ----------- | ---------: | ---------: | ---------: |
| Ridge       |     282.74 |     395.80 |     0.0679 |
| Lasso       |     295.62 |     410.06 |    -0.0008 |
| Elastic Net |     287.23 |     398.68 |     0.0542 |
| XGBoost     | **194.49** | **305.47** | **0.4451** |
| LightGBM    |     195.15 |     306.35 |     0.4419 |
| CatBoost    |     196.00 |     307.47 |     0.4379 |
| Stacking    |     197.63 |     308.58 |     0.4338 |

XGBoost achieved the strongest average CPCV performance among the final models.

## Generalization and Distribution Shift

Competition performance differed substantially from cross-validation performance, highlighting a potential distribution shift between the validation data and unseen competition data.

This motivates further investigation into airport-specific effects, temporal variation, and changing operational conditions.

## Repository Structure

```text
.
├── data/
├── notebooks/
├── src/
├── results/
├── requirements.txt
└── README.md
```

The HMM experiment may be stored separately from the final modeling pipeline to distinguish discontinued experiments from the final results.

## Key Findings

* Gradient-boosted tree models substantially outperformed linear baselines.
* XGBoost achieved the lowest mean CPCV RMSE.
* Stacking did not outperform the individual boosting models.
* The HMM approach was explored but discontinued.
* The validation-to-competition performance gap indicates challenges with distribution shift and generalization.

## Limitations

CPCV provides multiple chronological evaluation partitions but cannot guarantee that validation folds reproduce the hidden competition distribution. Model performance may therefore vary under unseen airports, time periods, and operational regimes.

## Acknowledgments

This project was developed as part of the **PRC Data Challenge** and is intended for research and educational purposes.

## License

MIT License/OPENGL

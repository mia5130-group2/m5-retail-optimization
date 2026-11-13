# Part 3 — Train and compare models

The comparison uses the same ten CA_1 products and 80 validation product-weeks, March 2–April 26, 2015. Coefficients are fitted through March 1, 2015. MAE and RMSE are in units per product-week; WAPE is 100 × total absolute error / total observed units. Positive bias means overprediction.

## Methods and tuning

- Seasonal naive: the completed weekly sales total 52 weeks earlier (364 days), an approximation to annual seasonality.
- Four-week average: the mean of the four preceding completed weekly totals.
- Proposed polynomial: a separate least-squares curve over calendar time for each product. Training-only chronological cross-validation selected degree **2** from degrees 2 and 3.
- Lag-based ridge regression: pooled regression using all 14 prepared numeric features and product indicators. Cross-validation selected alpha **100** from 0.1, 1, 10, 100, and 1000. Numeric scaling and product encoding are fitted on the training portion of each fold. The intercept is unpenalized.

Three expanding training folds each hold out the next eight weeks, yielding 240 historical comparisons per candidate. The separate eight-week validation window ranks the four fixed methods by MAE, with RMSE as the tie-breaker. All models make a new one-week-ahead forecast each week; observed earlier weeks may update lag inputs, while fitted coefficients stay fixed during each evaluation block. Forecasts stay fractional; negative raw predictions are retained in the audit table and clipped to zero for scoring.

## Validation results

| Method | MAE | RMSE | WAPE |
|---|---:|---:|---:|
| Lag-based ridge regression | 23.03 | 32.35 | 13.69% |
| Four-week average | 23.07 | 31.36 | 13.72% |
| Polynomial trend | 35.23 | 45.01 | 20.95% |
| Seasonal naive (52 weeks) | 41.71 | 51.48 | 24.80% |

The lowest validation MAE is **23.03** for **Lag-based ridge regression**. The lowest RMSE is **31.36** for **Four-week average**. The best MAE improves on the four-week average by only **0.17%** (0.040 units per product-week). Treat small differences as a close comparison, not evidence of a reliable advantage across seasons.

Relative to seasonal naive, the MAE-selected method reduces validation MAE by **44.80%** and RMSE by **37.15%**. These are validation results, not the proposal's final test-year success criterion.

The polynomial curve describes a smooth time trend and does not adapt to recent sales through lag inputs. Its extrapolation can be poor when demand changes or seasonality dominates. Ridge and the moving average use recent observed sales; the validation table determines their ranking without assuming a fitted model must outperform a benchmark.

## Scope and next step

Validation covers only eight spring weeks at one store. Product/quarter tables describe this window; the Q1/Q2 results cover partial quarters, assigned by target Monday. They do not establish full-year performance or statistical significance. The 56-week test period remains unscored and excluded from all tuning and selection. Prediction intervals and final held-out test evaluation are still required.

## Reproduce and reuse

Open [the Milestone 2 notebook](../../../M5_Milestone_2_Demand_Forecasting.ipynb) and run all cells using the project Python 3.15 environment. Part 3 is Section 4. The fitted models use the existing NumPy least-squares solver; no additional packages are needed. The polynomial's derived calendar time index is documented separately from the prepared lag-feature list in `model_feature_roles.csv`. Store, state, category, and department are omitted from ridge because they are constant or already determined by the selected product identifier.

The notebook's `m2_predict(state, input_rows)` reconstructs predictions from `fitted_models.json`, including stored scales and coefficients. Supply the prepared past-only features and target-week calendar fields. No target column is needed for prediction. Refitting after validation would change the fitted model and must be coordinated with later interval calibration.

## Files

- `cv_folds.csv`, `cv_candidate_scores.csv`, `hyperparameter_comparison.csv`: chronological tuning evidence.
- `validation_predictions.csv`: all 320 forecasts, actuals, raw forecasts, and errors.
- `validation_metrics.csv`: overall comparison and improvement versus seasonal naive.
- `validation_metrics_by_product.csv`, `validation_metrics_by_quarter.csv`: subgroup results.
- `fitted_models.json`, `model_selection.json`, `model_feature_roles.csv`: reusable parameters, selection policy, and input definitions.
- `model_training_checks.csv`: 10 passing checks, including independent numerical fits and test-data perturbation.
- `figures/validation_model_comparison.png`, `figures/validation_forecasts_by_product.png`: comparison and forecasts for all products.

Method references: [seasonal naive and chronological evaluation](https://otexts.com/fpp3/toolbox.html), [NumPy least squares](https://numpy.org/doc/stable/reference/generated/numpy.linalg.lstsq.html), and [the ridge objective](https://scikit-learn.org/stable/modules/linear_model.html#ridge-regression). The polynomial follows the Group 2 Milestone 1 proposal's least-squares curve-fitting method.

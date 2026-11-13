# Milestone 2 forecasting dataset

This dataset contains 2,193 weekly labeled examples for the ten agreed food products at CA_1. Predict next Monday-Sunday unit sales using information available by the preceding Sunday. The source is M5 Forecasting - Accuracy. All preparation is reproducible in [the Milestone 2 notebook](../../M5_Milestone_2_Demand_Forecasting.ipynb).

## Files

- `weekly_forecasting_panel.csv`: modeling table, one product and target week per row.
- `selected_products.csv`: fixed shortlist and its training-period selection evidence.
- `selected_daily_sales.csv`: observed daily sales and calendar fields for the selected series. It contains labels for the full observed history, so it must not be treated as a pre-cutoff-only table.
- `forecasting_split_summary.csv`: retained rows, dates, and exclusions by split.
- `forecasting_feature_roles.csv`: field meanings, availability, types, and predictor flags.
- `excluded_weekly_rows.csv`: individual training rows omitted with explicit reasons.
- `data_preparation_checks.csv`: 19 passing checks, including independent source reconciliation and future-data perturbations.
- `dataset_metadata.json`: feature lists, boundaries, assumptions, row counts, runtime versions, and source SHA-256 values.

## Use the declared predictors

Read `dataset_metadata.json` for the permitted prepared `feature_columns` and use `target_units` only as y. Model-specific subsets are documented in `models/model_feature_roles.csv`; the polynomial also derives a known calendar time index from target Monday. Filter the supplied `split` column into train, validation, and test. Fit encoders, scalers, imputers if later needed, and models using training data only; use validation for choices. Temporal folds must separate complete target weeks across every product. Do not shuffle rows or split products independently by row number. The table is sorted by target week, store, and product.

The training cutoff remains 2015-03-01. Validation spans 2015-03-02 through 2015-04-26; test spans 2015-04-27 through 2016-05-22. The first 52 complete weeks are feature warm-up. Rows without a price available at the origin are also excluded; all 577 exclusions are in training. All 80 validation and 560 test product-weeks are retained. Do not confuse calendar-window counts with the final retained training-row count.

## Timing and interpretation

Lags use the preceding 1, 2, 4, and 52 complete weeks. Rolling means and sample variability use shifted historical weekly sales. Every price is joined backward using the end of its retail week, with `price_information_date` and `price_age_days` retained. No target-week actual price is a predictor. Missing prices are never replaced with zero or filled backward from the future.

Target-week calendar and California SNAP counts are assumed known at the origin. M5 does not provide event announcement timestamps, so this remains an explicit calendar-availability assumption. Sales are a demand proxy and do not reveal stockouts or unmet demand. Genuine zeros are retained, including early observations that may predate listed-price history.

Forecasts are issued one week at a time. Earlier validation/test sales can become historical inputs after they occur; this is not a single year-ahead forecast. Report quarters by target Monday and retain whole weeks. Part 3 trains and compares point-forecast models; see [the model results](models/README.md). The test period remains unscored, and prediction intervals are a later step.

## Reproduce

Use the project's Python 3.15 environment and the pinned versions in requirements.txt. Open `M5_Milestone_2_Demand_Forecasting.ipynb` in the project folder and run all cells from a fresh kernel. Sections 1-3 include setup, the experiment, and dataset preparation; Milestone 1 does not need to run first. These final Milestone 2 files live under `outputs/milestone2/`; the files directly under `outputs/` describe the earlier exploratory experiment.

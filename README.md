# M5 Course Project — Milestones 1 and 2

**Group 2 - MIA5130A Systems Optimization and Management**

- [Milestone 1 — Data Exploration and Understanding](M5_Milestone_1_Data_Exploration_and_Understanding.ipynb): data exploration,
  illustrative baselines, and the report-ready Data Understanding draft.
- [Milestone 2 — Demand Forecasting](M5_Milestone_2_Demand_Forecasting.ipynb): the fixed CA_1 experiment, product
  selection audit, weekly features, leakage checks, trained models, and held-out test evaluation.

Both notebooks include their own setup and can run independently from a fresh
kernel. Run the notebook you need from top to bottom; Milestone 2 reads the raw
CSV files directly and does not depend on Milestone 1 outputs or kernel state.

## Setup

Use Python 3.15 with the NumPy and pandas versions pinned in `requirements.txt`. In a terminal
opened in this project folder:

```bash
python3.15 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m ipykernel install --user --name m5-exploration --display-name "Python 3.15 (M5 exploration)"
jupyter lab
```

Select **Python 3.15 (M5 exploration)** when opening either notebook. In VS Code,
select this kernel or the project's `.venv/bin/python` interpreter. After updating
packages, restart the kernel and run the selected notebook from top to bottom. A kernel
restart clears imports, so running a later cell first can produce `pd is not defined`.

The pinned versions replace older native NumPy/pandas builds that crashed during
date conversion on Python 3.15. Each setup cell checks compatibility before loading
those extensions. Calendar dates are explicitly parsed as ISO dates and normalized
to nanosecond resolution for consistent joins and CSV round-trips.

## Data location

Obtain the data from [Kaggle](https://www.kaggle.com/competitions/m5-forecasting-accuracy/data).
Place the five CSV files in `data/`, or set `DATA_DIR_OVERRIDE` in the first code cell.
Both notebooks also find `~/Downloads/m5-forecasting-accuracy` automatically.
Alternatively set `M5_DATA_DIR` to that folder before opening Jupyter.

Milestone 1 uses all five files:

- calendar.csv
- sales_train_evaluation.csv
- sales_train_validation.csv
- sell_prices.csv
- sample_submission.csv

Milestone 2 requires only `calendar.csv`, `sales_train_evaluation.csv`, and
`sell_prices.csv`.

Raw CSV files are read in place; they are not copied into this project or modified.
The full audit uses the wide sales matrix; only ten selected product-store series
are converted to long format. Allow approximately 1–2 GB of available working memory.

## Milestone 1 contents

- File inventory, checksums, schemas, and data dictionary.
- Full overlap verification between the two sales files.
- Keys, nulls, values, dates, hierarchy, and complete historical price-join audit.
- Sales distributions, trends, seasonality, events, SNAP, and price exploration.
- An illustrative ten-product subset selected with training-period data only.
- Weekly target and inputs, chronological splits, and independent timing checks.
- Rolling-origin validation baselines; the final test window is not scored.
- Inventory and routing limitations and a generated Data Understanding draft.

## Milestone 2 contents

- Fixed ten-product CA_1 scope, weekly target, chronological splits, and a work plan with deliverables.
- Selection audit using only information available by the March 1, 2015 training cutoff.
- Lagged sales, rolling statistics, historical prices, and known calendar features.
- Independent row-by-row reconciliation and future-sales/price perturbation checks.
- Final modeling panel, split labels, feature dictionary, exclusions, and reproduction instructions.

- Seasonal-naive, four-week-average, polynomial, and lag-based ridge forecasts.
- Chronological training-fold tuning, validation MAE/RMSE/WAPE, and forecasts for all ten products.
- Saved model parameters, input definitions, and checks excluding test data from selection.

- Final MAE/RMSE/WAPE on the same 560 test observations per model.
- Product, quarter, and product-quarter comparisons, with partial quarters identified.
- Assessment of the 10% MAE/RMSE reduction target against seasonal naive.
- Validation-only 80%/95% interval calibration frozen before test scoring.

**Parts 1–4 are implemented.** Part 5 (test intervals, coverage, and uncertainty plots)
and Part 6 (the final submission report) remain unfinished. See [the validation
comparison](outputs/milestone2/models/README.md) and [the held-out test
evaluation](outputs/milestone2/evaluation/README.md) for results and limitations.
The test comparison retains the original fitted models and validation-selected ridge;
test outcomes do not drive tuning, refitting, or selection.

## Outputs

Milestone 1 writes directly under `outputs/`: eight figure PNGs, compact profile/quality tables, the selected
daily sales table, a weekly forecasting panel, `data_profile.json`, exact runtime
versions, and `data_understanding.md`. These describe the earlier exploratory experiment.

Milestone 2 writes its final dataset and supporting tables under `outputs/milestone2/`.
See [the dataset handoff](outputs/milestone2/README.md) for predictor definitions
and timing assumptions. Model outputs, figures, fitted parameters, and the validation
comparison are in `outputs/milestone2/models/`. Final test forecasts, metric tables,
target assessments, figures, frozen settings, and checks are in
`outputs/milestone2/evaluation/`. `outputs/milestone2/notebook_validation.json`
records the fresh-kernel execution and checks. Running either notebook regenerates
its analytical outputs; the validation record documents the verified saved copy.
The raw dataset remains the source of truth; exported subsets are analysis products.

Milestone 1 preserves the original exploratory scope; Milestone 2 contains the
fixed ten-product experiment and a work plan organized by activity and deliverable.
Milestone 2 includes trained point-forecast models, validation comparisons, and final test evaluation.
Inventory and routing optimization remain later milestones.
M5 describes U.S. retail activity: SNAP is defined in the notebook, and prices are in USD.

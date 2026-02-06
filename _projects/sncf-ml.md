---
layout: page
title: Real-Time Train Crowding Forecasting (SNCF-Transilien)
description: Tabular ML forecasting of one-station-ahead train occupancy using contextual + lag features from infrared door sensors.
img: assets/img/sncf-results.png
importance: 3
category: work
---

### Overview
Forecasted **one-station-ahead train occupancy** (crowding factor in \[0,1\]) for SNCF-Transilien using tabular ML. The dataset includes contextual variables (date/train/station/hour/way/composition) plus lagged occupancy features describing both (i) previous trains at the same station and (ii) previous stops for the same train.

**Repo (course workspace):** https://github.com/ArthurMbraga/machine-learning-for-networks

---

## Data & task
A single “stop” is identified by (train, station, day). The challenge provides:
- **Xtrain:** 31,119 rows, 12 columns  
- **Xtest:** 13,752 rows, 12 columns  
- **Ytrain:** target occupancy rate (crowding factor)

**Features**
- Context: `date`, `train`, `station`, `hour`, `way`, `composition`
- Lags:
  - previous trains at same station: `t1s0`, `t2s0`, `t3s0`
  - previous stations for the same train: `t0s1`, `t0s2`, `t0s3`

---

## Methods
### Missing values
Tested multiple strategies for missing lag values (drop samples; constant fill; mean fill; most-frequent fill), selecting the approach that improved RMSE on baseline regression.

### Feature engineering
Created aggregated lag features:
- `avg_lag_train = mean(t1s0, t2s0, t3s0)`
- `avg_lag_station = mean(t0s1, t0s2, t0s3)`

---

## Models & results
Benchmarked:
- Linear regression (with parameter search): RMSE ≈ 0.059
- Polynomial regression: best feature subset RMSE ≈ 0.0261
- Neural network (MLP): RMSE ≈ 0.0414 down to ≈ 0.0381 (depending on architecture)
- **Random Forest (best): RMSE = 0.0229**

Baseline benchmark RMSE: **0.158**, yielding **~84.7% improvement** with Random Forest.

{% include figure.liquid
  path="assets/img/sncf-results.png"
  class="img-fluid rounded z-depth-1"
  caption="Random Forest results: RMSE = 0.0229. Feature importance highlights lag features (e.g., t0s1, t0s2), and predictions align closely with ground truth on the test set."
%}

---

## Takeaways
- Lag features dominate: prior-station occupancy (`t0s1`, `t0s2`) and aggregated lags are among the most informative predictors.
- The mapping from lags to occupancy is not strictly linear, motivating tree/ensemble models.

---

### Tools
Python • scikit-learn (Random Forest, Linear/Polynomial Regression) • MLP

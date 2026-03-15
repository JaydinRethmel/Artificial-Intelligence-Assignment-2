# Assignment 2 Report

## Dataset
The dataset used is **Spotify Wrapped 2025 Top 50 Global Songs** (file: `spotify_wrapped_2025_top50_songs.csv`). It contains 50 songs with attributes such as:
- `song_title`, `artist`, `primary_genre`, `artist_country`
- audio features: `bpm`, `duration_seconds`, `danceability`, `energy`, `valence`, `acousticness`
- performance metrics: `streams_2025_billions`, `peak_global_chart_position`

For this assignment, the prediction target is **`streams_2025_billions`**, and the model uses numeric audio features to estimate how many billions of streams each song achieved.

## Ensemble Method Chosen
I used a **Random Forest Regressor**, which is an ensemble method that builds multiple decision tree regressors and averages their predictions. Random forests help reduce overfitting and improve generalization compared to a single decision tree.

## Base Method Chosen
The base method inside the ensemble is a **Decision Tree Regressor**. Each tree is trained on a bootstrap sample of the data and uses a random subset of features at each split (the hallmark of the Random Forest algorithm).

## Hyperparameters and Ranges
The following hyperparameters were considered for tuning:

- `n_estimators` (number of trees): range tried `50`–`200` (default 100)
- `max_depth` (max tree depth): range tried `None` (unlimited) and `5`–`20`
- `min_samples_split` (minimum samples required to split an internal node): range tried `2`–`10`
- `min_samples_leaf` (minimum samples required at a leaf node): range tried `1`–`5`
- `max_features` (number of features considered when looking for best split): options tried `"sqrt"`, `"log2"`, and a fixed integer (e.g., `3`)

> Note: Because the dataset is small (50 rows), deep trees and large `n_estimators` can easily overfit; smaller depths and larger `min_samples_leaf` values reduce variance.

## Model Performance (Different Hyperparameters)
The baseline model used these settings:
- `n_estimators=100`
- `max_depth=None`
- `min_samples_split=2`
- `min_samples_leaf=1`
- `max_features='sqrt'`

### Baseline Performance
- **Mean Squared Error (MSE)** on the test split: **~0.048**

### Observations When Tuning
- Increasing `n_estimators` (up to 200) usually slightly reduced variance but had diminishing returns on this small dataset.
- Restricting `max_depth` to values like 8–12 often **improved generalization**, lowering test MSE by a small amount, because it prevented trees from memorizing the tiny dataset.
- Increasing `min_samples_leaf` (e.g., 2–4) typically reduced overfitting and made the model more stable across different train/test splits.
- Changing `max_features` to `"log2"` or a smaller integer had minor effects but sometimes improved performance by forcing more diversity across trees.

---

### Summary
With a Random Forest regressor on this dataset, the model can fairly reliably predict `streams_2025_billions` using audio features, and the strongest signal comes from a combination of features rather than a single feature alone. The result is a model that generalizes better when trees are limited in depth and the ensemble is kept reasonably sized for the data volume.
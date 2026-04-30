# Modeling — Claude Code Execution Guide

Train, tune, and evaluate models autonomously. Always start with a baseline. Choose model based on problem type specified in CLAUDE.md.

---

## Model Selection by Problem Type

| Problem | Baseline | Primary |
|---|---|---|
| Binary classification | LogisticRegression | XGBoost |
| Multi-class | LogisticRegression | XGBoost / LightGBM |
| Regression | LinearRegression | XGBoost / LightGBM |
| Clustering | KMeans (K=3) | KMeans with elbow + silhouette |
| Time series | Naive forecast | Prophet / ARIMA |

---

## Classification
```python
from sklearn.model_selection import cross_val_score, RandomizedSearchCV, StratifiedKFold
from sklearn.linear_model import LogisticRegression
from xgboost import XGBClassifier
from sklearn.metrics import classification_report, roc_auc_score
import numpy as np

cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

# Baseline
baseline = LogisticRegression(max_iter=1000, random_state=42)
baseline_scores = cross_val_score(baseline, X_train, y_train, cv=cv, scoring='roc_auc')
print(f"Baseline AUC: {baseline_scores.mean():.4f} (+/- {baseline_scores.std():.4f})")

# Primary model
model = XGBClassifier(
    n_estimators=300, learning_rate=0.05, max_depth=6,
    subsample=0.8, colsample_bytree=0.8,
    eval_metric='logloss', random_state=42
)

# Tuning
param_grid = {
    'max_depth': [3, 5, 6, 8],
    'learning_rate': [0.01, 0.05, 0.1],
    'n_estimators': [100, 300, 500],
    'subsample': [0.7, 0.8, 1.0]
}
search = RandomizedSearchCV(model, param_grid, n_iter=30, cv=cv,
    scoring='roc_auc', n_jobs=-1, random_state=42)
search.fit(X_train, y_train)
print(f"Best AUC: {search.best_score_:.4f}")
print(f"Best params: {search.best_params_}")
```

---

## Regression
```python
from sklearn.linear_model import LinearRegression
from xgboost import XGBRegressor
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score

baseline = LinearRegression()
baseline_scores = cross_val_score(baseline, X_train, y_train, cv=5, scoring='r2')
print(f"Baseline R2: {baseline_scores.mean():.4f}")

model = XGBRegressor(n_estimators=300, learning_rate=0.05, random_state=42)
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
print(f"RMSE: {np.sqrt(mean_squared_error(y_test, y_pred)):.4f}")
print(f"MAE: {mean_absolute_error(y_test, y_pred):.4f}")
print(f"R2: {r2_score(y_test, y_pred):.4f}")
```

---

## Clustering
```python
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score

inertia, sil_scores = [], []
K_range = range(2, 9)

for k in K_range:
    km = KMeans(n_clusters=k, random_state=42, n_init=10)
    labels = km.fit_predict(X_scaled)
    inertia.append(km.inertia_)
    sil_scores.append(silhouette_score(X_scaled, labels))
    print(f"K={k} silhouette={sil_scores[-1]:.4f}")

best_k = list(K_range)[np.argmax(sil_scores)]
print(f"Best K by silhouette: {best_k}")
# STOP HERE — report K options to Jerry before fitting final model
```

---

## Save Pipeline
```python
import joblib
import os
os.makedirs('Output', exist_ok=True)

joblib.dump(search.best_estimator_, 'Output/model.pkl')
print("Model saved to Output/model.pkl")
```

---

## Decision Rules
- Always run baseline before primary model
- If baseline beats primary → keep baseline, report why
- Clustering: stop after elbow/silhouette and report K options to Jerry
- Same error 3 times during training → stop and report to Jerry
- Class imbalance > 80:20 → use class_weight='balanced'

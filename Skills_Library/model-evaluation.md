# Model Evaluation — Claude Code Execution Guide

Evaluate trained model against business thresholds. Produce go/no-go recommendation autonomously.

---

## Classification Evaluation
```python
from sklearn.metrics import (classification_report, roc_auc_score,
    confusion_matrix, ConfusionMatrixDisplay, RocCurveDisplay)
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
import os
os.makedirs('Output', exist_ok=True)

y_pred = model.predict(X_test)
y_proba = model.predict_proba(X_test)[:, 1]

print(classification_report(y_test, y_pred))
print(f"AUC-ROC: {roc_auc_score(y_test, y_proba):.4f}")

# Confusion matrix
fig, ax = plt.subplots(figsize=(6, 5))
ConfusionMatrixDisplay.from_predictions(y_test, y_pred, ax=ax)
plt.savefig('Output/confusion_matrix.png', dpi=100, bbox_inches='tight')
plt.close()

# ROC curve
fig, ax = plt.subplots(figsize=(6, 5))
RocCurveDisplay.from_predictions(y_test, y_proba, ax=ax)
plt.savefig('Output/roc_curve.png', dpi=100, bbox_inches='tight')
plt.close()
```

---

## Regression Evaluation
```python
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score
import numpy as np

y_pred = model.predict(X_test)
rmse = np.sqrt(mean_squared_error(y_test, y_pred))
mae = mean_absolute_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print(f"RMSE: {rmse:.4f}")
print(f"MAE: {mae:.4f}")
print(f"R2: {r2:.4f}")

# Residuals plot
fig, ax = plt.subplots(figsize=(8, 5))
ax.scatter(y_pred, y_test - y_pred, alpha=0.3)
ax.axhline(0, color='red', linestyle='--')
ax.set_xlabel('Predicted')
ax.set_ylabel('Residuals')
ax.set_title('Residuals vs Predicted')
plt.savefig('Output/residuals.png', dpi=100, bbox_inches='tight')
plt.close()
```

---

## SHAP Interpretation
```python
import shap
import pandas as pd

explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_test)

# Feature importance
importance_df = pd.DataFrame({
    'feature': X_test.columns if hasattr(X_test, 'columns') else range(X_test.shape[1]),
    'mean_shap': abs(shap_values).mean(axis=0)
}).sort_values('mean_shap', ascending=False)

print("Top 10 features:")
print(importance_df.head(10))
importance_df.to_csv('Output/feature_importance.csv', index=False)
```

---

## Go / No-Go Verdict
```python
# Compare against success criteria from CLAUDE.md
# Adjust metric and threshold based on project

auc = roc_auc_score(y_test, y_proba)
threshold = 0.75  # from CLAUDE.md success criteria

if auc >= threshold:
    verdict = "GO"
    print(f"✅ GO — AUC {auc:.4f} meets threshold {threshold}")
else:
    verdict = "NO-GO"
    print(f"❌ NO-GO — AUC {auc:.4f} below threshold {threshold}")
    print("Action: try different model or feature engineering before proceeding")
```

---

## Decision Rules
- AUC < success criteria → NO-GO, try next model
- Top SHAP feature doesn't make business sense → flag potential data leakage
- Test performance >> train performance → flag data leakage
- All evaluation outputs saved to Output/

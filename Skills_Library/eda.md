# EDA — Claude Code Execution Guide

Run structured exploratory data analysis. Make decisions autonomously. Connect every finding to the business problem stated in CLAUDE.md.

---

## Step 1 — Univariate Analysis
```python
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
import seaborn as sns
import os
os.makedirs('Output', exist_ok=True)

# Numeric columns
for col in df.select_dtypes(include='number').columns:
    fig, axes = plt.subplots(1, 2, figsize=(12, 4))
    axes[0].hist(df[col].dropna(), bins=30, edgecolor='black')
    axes[0].set_title(f'{col} — Distribution')
    df.boxplot(column=col, ax=axes[1])
    axes[1].set_title(f'{col} — Boxplot')
    plt.tight_layout()
    plt.savefig(f'Output/eda_{col}_dist.png', dpi=100, bbox_inches='tight')
    plt.close()
    print(f"{col}: mean={df[col].mean():.2f}, median={df[col].median():.2f}, skew={df[col].skew():.2f}")

# Categorical columns
for col in df.select_dtypes(include='object').columns:
    vc = df[col].value_counts().head(15)
    fig, ax = plt.subplots(figsize=(10, 4))
    vc.plot(kind='bar', ax=ax)
    ax.set_title(f'{col} — Top Values')
    plt.tight_layout()
    plt.savefig(f'Output/eda_{col}_counts.png', dpi=100, bbox_inches='tight')
    plt.close()
    print(f"{col}: {df[col].nunique()} unique values")
```

---

## Step 2 — Bivariate Analysis
```python
# Correlation matrix for numeric columns
numeric_cols = df.select_dtypes(include='number').columns
if len(numeric_cols) > 1:
    corr = df[numeric_cols].corr()
    fig, ax = plt.subplots(figsize=(10, 8))
    sns.heatmap(corr, annot=True, fmt='.2f', cmap='RdYlGn', ax=ax, linewidths=0.5)
    ax.set_title('Correlation Matrix')
    plt.tight_layout()
    plt.savefig('Output/eda_correlation.png', dpi=100, bbox_inches='tight')
    plt.close()

    # Flag high correlations
    for i in range(len(corr.columns)):
        for j in range(i+1, len(corr.columns)):
            if abs(corr.iloc[i, j]) > 0.8:
                print(f"HIGH CORRELATION: {corr.columns[i]} vs {corr.columns[j]} = {corr.iloc[i,j]:.2f}")
```

---

## Step 3 — Target Variable Analysis (if applicable)
```python
# If a target variable is specified in CLAUDE.md, analyze it
# Classification: check class balance
# Regression: check distribution and outliers

# Example for classification target
# target_col = 'your_target'
# print(df[target_col].value_counts(normalize=True))
# if imbalance > 80:20 → flag it
```

---

## Step 4 — Hypothesis Testing
```python
from scipy import stats

# Test key hypotheses stated in CLAUDE.md Game Plan
# Example: does group A spend more than group B?
# t-test for numeric, chi-square for categorical

# Always report: test used, p-value, effect size, business interpretation
```

---

## Step 5 — EDA Summary
```python
print("=== EDA SUMMARY ===")
print(f"Dataset: {df.shape[0]:,} rows, {df.shape[1]} columns")
print(f"Numeric columns: {list(df.select_dtypes(include='number').columns)}")
print(f"Categorical columns: {list(df.select_dtypes(include='object').columns)}")
print(f"Date columns: {list(df.select_dtypes(include='datetime').columns)}")
print("\nKey findings:")
# Print skewed columns
for col in df.select_dtypes(include='number').columns:
    if abs(df[col].skew()) > 1:
        print(f"  - {col} is skewed ({df[col].skew():.2f}) → consider log transform")
```

---

## Decision Rules
- Skewness > 1 → flag for log transform in feature engineering
- Correlation > 0.8 between two features → flag potential multicollinearity
- Class imbalance > 80:20 → flag before modeling
- Outliers beyond 3 std → flag, do not remove automatically
- All charts saved to Output/ with prefix eda_

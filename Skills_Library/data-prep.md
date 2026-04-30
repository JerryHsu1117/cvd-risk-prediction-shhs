# Data Prep — Claude Code Execution Guide

Load, clean, and validate the dataset. Make decisions autonomously based on what you find. Do not ask for confirmation unless a Decision Rule triggers.

---

## Step 1 — Load Data
```python
import pandas as pd
import numpy as np

# Detect file type and load accordingly
# .xlsx → pd.read_excel()
# .csv → pd.read_csv()
# .json → pd.read_json()

df = pd.read_excel('Dataset/your_file.xlsx')  # adjust path from CLAUDE.md
print(f"Shape: {df.shape}")
print(f"Columns: {df.columns.tolist()}")
print(df.head())
print(df.dtypes)
```

---

## Step 2 — Data Quality Audit
```python
# Missing values
missing = df.isnull().sum()
missing_pct = (missing / len(df) * 100).round(2)
print(pd.DataFrame({'Missing': missing, 'Pct': missing_pct}))

# Duplicates
print(f"Duplicate rows: {df.duplicated().sum()}")

# Basic stats
print(df.describe())
```

---

## Step 3 — Clean Data (Autonomous Decisions)

**Missing values:**
```python
for col in df.columns:
    pct = df[col].isnull().mean()
    if pct == 0:
        pass  # no action needed
    elif pct < 0.05:
        # drop rows — small loss
        df = df.dropna(subset=[col])
    elif pct < 0.20:
        # impute — median for numeric, mode for categorical
        if df[col].dtype in ['float64', 'int64']:
            df[col].fillna(df[col].median(), inplace=True)
        else:
            df[col].fillna(df[col].mode()[0], inplace=True)
    else:
        # >20% missing — STOP and report
        print(f"WARNING: {col} has {pct:.1%} missing. Review before proceeding.")
        # Do not drop column automatically — flag it
```

**Duplicates:**
```python
df = df.drop_duplicates()
print(f"After dedup: {df.shape}")
```

**Data types:**
```python
# Fix obvious type issues
# Dates → datetime
# IDs → string or int
# Prices/quantities → float or int
for col in df.select_dtypes('object').columns:
    try:
        df[col] = pd.to_datetime(df[col])
        print(f"Converted {col} to datetime")
    except:
        pass
```

**Invalid values:**
```python
# Negative quantities or prices → remove
for col in df.select_dtypes(include='number').columns:
    if any(word in col.lower() for word in ['price', 'quantity', 'amount', 'revenue', 'sales']):
        before = len(df)
        df = df[df[col] >= 0]
        print(f"Removed {before - len(df)} rows with negative {col}")
```

---

## Step 4 — Save Clean Dataset
```python
import os
os.makedirs('Output', exist_ok=True)

df.to_csv('Output/clean_data.csv', index=False)
print(f"Clean dataset saved: {df.shape[0]:,} rows, {df.shape[1]} columns")
```

---

## Decision Rules
- Missing > 20% in any column → flag it, do not drop automatically
- More than 30% rows removed during cleaning → stop and report to Jerry
- All numeric values in a column are identical → flag as near-zero variance
- Date column detected → always convert to datetime, extract year/month/day_of_week

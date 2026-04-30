# Python — Claude Code Execution Guide

Python execution reference for data analysis and pipelines. Write clean, efficient, runnable code.

---

## Core Patterns

### File I/O
```python
import pandas as pd
import os

# Read
df = pd.read_csv('Dataset/file.csv')
df = pd.read_excel('Dataset/file.xlsx')
df = pd.read_json('Dataset/file.json')

# Write
os.makedirs('Output', exist_ok=True)
df.to_csv('Output/result.csv', index=False)
df.to_excel('Output/result.xlsx', index=False)
```

### Data Manipulation
```python
# Filter
df_filtered = df[df['col'] > threshold]

# Group and aggregate
summary = df.groupby('col').agg({'value': ['sum', 'mean', 'count']}).reset_index()
summary.columns = ['col', 'total', 'avg', 'count']

# Merge
merged = pd.merge(df1, df2, on='key', how='left')

# Apply function
df['new_col'] = df['col'].apply(lambda x: x * 2 if x > 0 else 0)

# Pivot
pivot = df.pivot_table(values='value', index='row_var', columns='col_var', aggfunc='sum')
```

### Error Handling
```python
try:
    result = risky_operation()
except FileNotFoundError as e:
    print(f"File not found: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
    raise
```

### Progress Tracking
```python
from tqdm import tqdm

for item in tqdm(items, desc="Processing"):
    process(item)
```

---

## Code Quality Rules
- Use f-strings not .format() or %
- Always use `os.makedirs('dir', exist_ok=True)` before writing files
- Print shape and sample after every major transformation
- Use descriptive variable names — no single letters except loop indices
- Add inline comments for non-obvious logic only
- Never hardcode file paths — read from CLAUDE.md or use relative paths

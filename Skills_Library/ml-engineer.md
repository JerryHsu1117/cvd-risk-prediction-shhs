# ML Engineer — Claude Code Execution Guide

Execute feature engineering autonomously. Read the dataset directly, make decisions based on what you find. Do not ask for confirmation unless a Decision Rule triggers.

---

## Feature Engineering

### Detect and handle skewness
```python
import numpy as np

for col in df.select_dtypes(include='number').columns:
    skew = df[col].skew()
    if abs(skew) > 1:
        df[f'{col}_log'] = np.log1p(df[col])  # log transform — right skew detected
        print(f"Log transformed: {col} (skew={skew:.2f})")
```

### Encoding
```python
from sklearn.preprocessing import OneHotEncoder, OrdinalEncoder

for col in df.select_dtypes(include='object').columns:
    n_unique = df[col].nunique()
    if n_unique <= 10:
        # OneHotEncoder — low cardinality
        dummies = pd.get_dummies(df[col], prefix=col, drop_first=True)
        df = pd.concat([df, dummies], axis=1)
        df.drop(col, axis=1, inplace=True)
        print(f"One-hot encoded: {col} ({n_unique} categories)")
    else:
        # OrdinalEncoder — high cardinality
        from sklearn.preprocessing import OrdinalEncoder
        df[col] = OrdinalEncoder().fit_transform(df[[col]])
        print(f"Ordinal encoded: {col} ({n_unique} categories)")
```

### Scaling
```python
from sklearn.preprocessing import StandardScaler

numeric_cols = df.select_dtypes(include='number').columns.tolist()
scaler = StandardScaler()
df_scaled = scaler.fit_transform(df[numeric_cols])
print(f"Scaled {len(numeric_cols)} numeric features")
```

### DateTime features
```python
for col in df.select_dtypes(include='datetime').columns:
    df[f'{col}_year'] = df[col].dt.year
    df[f'{col}_month'] = df[col].dt.month
    df[f'{col}_dayofweek'] = df[col].dt.dayofweek
    df[f'{col}_is_weekend'] = df[col].dt.dayofweek.isin([5, 6]).astype(int)
    print(f"Extracted datetime features from: {col}")
```

### sklearn Pipeline
```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer

numeric_features = df.select_dtypes(include='number').columns.tolist()
categorical_features = df.select_dtypes(include='object').columns.tolist()

numeric_transformer = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())
])

categorical_transformer = Pipeline([
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('encoder', OneHotEncoder(handle_unknown='ignore', sparse_output=False))
])

preprocessor = ColumnTransformer([
    ('num', numeric_transformer, numeric_features),
    ('cat', categorical_transformer, categorical_features)
])
```

---

## Decision Rules
- Skewness > 1 → log1p transform
- Categorical < 10 unique → OneHotEncoder
- Categorical >= 10 unique → OrdinalEncoder
- Missing numeric → median imputation
- Missing categorical → most frequent imputation
- DateTime column → always extract year, month, day_of_week, is_weekend

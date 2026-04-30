# Chart Advisor — Claude Code Execution Guide

Select and generate the right chart for each business question. All charts saved to Output/.

---

## Chart Selection Rules

| Question Type | Chart |
|---|---|
| Distribution of one variable | Histogram + KDE |
| Compare groups | Bar chart or Box plot |
| Relationship between two numeric | Scatter plot |
| Correlation across many variables | Heatmap |
| Trend over time | Line chart |
| Part-to-whole | Bar chart (stacked or 100%) |
| Segment profiles | Heatmap or Radar |
| Geographic | Choropleth or bar by region |

---

## Standard Chart Templates
```python
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
import seaborn as sns
import os
os.makedirs('Output', exist_ok=True)

plt.style.use('seaborn-v0_8-whitegrid')
sns.set_palette('Set2')

# --- Distribution ---
def plot_distribution(df, col, filename):
    fig, axes = plt.subplots(1, 2, figsize=(12, 4))
    axes[0].hist(df[col].dropna(), bins=30, edgecolor='black')
    axes[0].set_title(f'{col} — Distribution')
    df.boxplot(column=col, ax=axes[1])
    axes[1].set_title(f'{col} — Boxplot')
    plt.tight_layout()
    plt.savefig(f'Output/{filename}', dpi=150, bbox_inches='tight')
    plt.close()

# --- Bar chart ---
def plot_bar(data, x, y, title, filename, rotate=False):
    fig, ax = plt.subplots(figsize=(10, 5))
    ax.bar(data[x], data[y], color=sns.color_palette('Set2'))
    ax.set_title(title)
    if rotate:
        plt.xticks(rotation=30, ha='right')
    plt.tight_layout()
    plt.savefig(f'Output/{filename}', dpi=150, bbox_inches='tight')
    plt.close()

# --- Heatmap ---
def plot_heatmap(data, title, filename, annot=True):
    fig, ax = plt.subplots(figsize=(10, 6))
    norm = (data - data.min()) / (data.max() - data.min())
    sns.heatmap(norm, annot=data.round(1) if annot else False,
                fmt='g', cmap='RdYlGn_r', ax=ax, linewidths=0.5)
    ax.set_title(title)
    plt.tight_layout()
    plt.savefig(f'Output/{filename}', dpi=150, bbox_inches='tight')
    plt.close()

# --- Line chart ---
def plot_line(df, x, y, title, filename):
    fig, ax = plt.subplots(figsize=(12, 5))
    ax.plot(df[x], df[y], linewidth=2)
    ax.set_title(title)
    plt.tight_layout()
    plt.savefig(f'Output/{filename}', dpi=150, bbox_inches='tight')
    plt.close()

# --- Scatter ---
def plot_scatter(df, x, y, hue=None, title='', filename='scatter.png'):
    fig, ax = plt.subplots(figsize=(8, 6))
    if hue:
        for label in df[hue].unique():
            mask = df[hue] == label
            ax.scatter(df.loc[mask, x], df.loc[mask, y], label=label, alpha=0.4, s=15)
        ax.legend()
    else:
        ax.scatter(df[x], df[y], alpha=0.4, s=15)
    ax.set_xlabel(x)
    ax.set_ylabel(y)
    ax.set_title(title)
    plt.tight_layout()
    plt.savefig(f'Output/{filename}', dpi=150, bbox_inches='tight')
    plt.close()
```

---

## Decision Rules
- Always use `matplotlib.use('Agg')` — no display available in Claude Code
- Always `plt.close()` after saving — prevents memory leaks
- Chart title must state the insight, not just the variable name
  - ❌ "Revenue by Segment"
  - ✅ "Champions drive 68% of total revenue"
- All charts saved to Output/ with descriptive filenames
- DPI=150 for presentation-ready quality

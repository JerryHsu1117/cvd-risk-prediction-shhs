# Storytelling — Claude Code Execution Guide

Structure findings into a compelling narrative. Every chart must answer a question. Every section must connect to the business problem.

---

## Narrative Structure
```python
narrative_framework = {
    "Hook": "Start with the business problem, not the data",
    "Context": "What data did we have and what did we do with it",
    "Conflict": "What was surprising or counterintuitive in the findings",
    "Resolution": "What the findings mean and what to do about it",
    "Call to Action": "3 specific, prioritized recommendations"
}

for section, description in narrative_framework.items():
    print(f"{section}: {description}")
```

---

## Chart Titling Rules
```python
# Every chart title must state the insight, not just the variables

bad_titles = [
    "Revenue by Segment",
    "Distribution of Recency",
    "Customer Count"
]

good_titles = [
    "Champions drive 68% of revenue despite being only 15% of customers",
    "At-Risk customers haven't purchased in over 6 months on average",
    "Lost segment is the largest by count but contributes least to revenue"
]

# Apply to all charts
for chart, title in zip(charts, good_titles):
    chart.set_title(title)
```

---

## Presentation Outline Generator
```python
def generate_slide_outline(segments, key_metrics, recommendations):
    outline = f"""
SLIDE 1: Title
SLIDE 2: The Problem — "We treat all customers the same. We shouldn't."
SLIDE 3: The Data — {len(df):,} transactions, {df['CustomerID'].nunique():,} customers
SLIDE 4: Our Approach — {method}
SLIDE 5: Meet Your Customers — {len(segments)} distinct segments
SLIDE 6: Segment Deep Dive — profiles and behavioral differences
SLIDE 7: The Business Opportunity — revenue concentration and at-risk value
SLIDE 8: Recommendation 1 — {recommendations[0]}
SLIDE 9: Recommendation 2 — {recommendations[1]}
SLIDE 10: Recommendation 3 — {recommendations[2]}
SLIDE 11: Limitations & Next Steps
SLIDE 12: Q&A
"""
    with open('Output/slide_outline.md', 'w') as f:
        f.write(outline)
    print("✅ Slide outline saved to Output/slide_outline.md")
    return outline
```

---

## Decision Rules
- Never start with methodology — start with the business problem
- Each slide should have one key message
- Use the "So what?" test on every finding — if you can't answer it, cut the finding
- Recommendations must be specific, actionable, and prioritized by impact
- Save narrative outline to Output/slide_outline.md

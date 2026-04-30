# Report — Claude Code Execution Guide

Assemble the final deliverables: notebook structure, README, and output summary. This is always the last step.

---

## Notebook Structure Check
```python
# Verify notebook has required structure
required_cells = [
    "Project Summary header (title, author, course, date, overview)",
    "Each step: markdown (description) → code → markdown (insight)",
    "Final cell: Recommendations & Conclusion"
]

for cell in required_cells:
    print(f"✅ Required: {cell}")
```

---

## Generate README.md
```python
import os

def generate_readme(project_name, author, course, objective, dataset_desc,
                    method, key_findings, how_to_run):
    readme = f"""# {project_name}

**Author:** {author}
**Course:** {course}
**Institution:** Loyola Marymount University

---

## Objective
{objective}

## Dataset
{dataset_desc}

## Method
{method}

## Key Findings
{chr(10).join(f'- {f}' for f in key_findings)}

## How to Run
{how_to_run}

## Output Files
| File | Description |
|---|---|
| `notebooks/analysis.ipynb` | Main analysis notebook |
| `Output/report.md` | Written report |
| `Output/slide_outline.md` | Presentation outline |

## Tech Stack
- Python 3.x
- pandas, numpy, sklearn, matplotlib, seaborn
"""
    with open('README.md', 'w') as f:
        f.write(readme)
    print("✅ README.md saved to project root")

# Call with actual project values
generate_readme(
    project_name="Project Title",
    author="Jerry Hsu",
    course="BSAN XXXX — Course Name",
    objective="One sentence describing what this project does and why",
    dataset_desc="Dataset name, size, time period, source",
    method="Method chosen and brief rationale",
    key_findings=["Finding 1", "Finding 2", "Finding 3"],
    how_to_run="1. Open notebooks/analysis.ipynb\n2. Run all cells top to bottom"
)
```

---

## Final Output Checklist
```python
import os

expected_outputs = [
    'README.md',
    'notebooks/analysis.ipynb',
    'Output/report.md',
    'Output/slide_outline.md'
]

print("=== Final Output Checklist ===")
all_present = True
for f in expected_outputs:
    exists = os.path.exists(f)
    status = "✅" if exists else "❌ MISSING"
    print(f"{status} {f}")
    if not exists:
        all_present = False

if all_present:
    print("\n✅ All outputs present — ready for GitHub upload")
else:
    print("\n❌ Some outputs missing — do not push to GitHub yet")
```

---

## GitHub Upload Prompt
```python
print("=" * 50)
print("All analysis complete. Ready to upload to GitHub.")
print("Do you want to upload the notebook and README to GitHub? (yes/no)")
# STOP HERE — wait for Jerry's confirmation
# If yes → follow github-organizer.md
# If no → print "Skipping GitHub upload. All files saved locally."
```

---

## Decision Rules
- README always goes in project root, not Output/
- Always run final checklist before asking about GitHub
- Never push without Jerry's explicit confirmation
- Notebook must have project summary header and conclusions cell

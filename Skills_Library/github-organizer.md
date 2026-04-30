# GitHub Organizer — Claude Code Execution Guide

Package project and push to GitHub. Always ask Jerry before pushing.

---

## Step 1 — Check Git Status
```bash
git status
git log --oneline -5 2>/dev/null || echo "No commits yet"
```

---

## Step 2 — Initialize and Commit
```bash
# Initialize if not already a repo
git init

# Add all project files except data and sensitive files
cat > .gitignore << 'EOF'
Dataset/
*.xlsx
*.csv
*.pkl
*.pth
__pycache__/
.env
*.pyc
EOF

git add .
git commit -m "Initial commit: [Project Name] — [brief description]"
```

---

## Step 3 — Ask Jerry Before Pushing
```python
print("=" * 50)
print("Ready to push to GitHub.")
print("Files staged:")
import subprocess
result = subprocess.run(['git', 'status', '--short'], capture_output=True, text=True)
print(result.stdout)
print("=" * 50)
print("Do you want to push to GitHub? (yes/no)")
# STOP HERE — wait for Jerry's confirmation
```

---

## Step 4 — Push (Only After Confirmation)
```bash
# Add remote if not exists
git remote get-url origin 2>/dev/null || git remote add origin https://github.com/JerryHsu1117/[repo-name].git

# Push
git branch -M main
git push -u origin main
echo "✅ Pushed to GitHub successfully"
```

---

## Repo Naming Convention
Format: `topic-method-context` (all lowercase, hyphens only)

Examples:
- `customer-segmentation-kmeans`
- `sales-forecasting-prophet`
- `churn-prediction-xgboost`
- `rfm-analysis-clustering`

---

## Decision Rules
- NEVER push raw data files (xlsx, csv) to GitHub
- NEVER push model weights (.pkl, .pth) unless explicitly asked
- Always create .gitignore before first commit
- Always stop and ask Jerry before pushing
- Commit message format: "Initial commit: [topic] — [method]"

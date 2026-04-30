# CVD Risk Prediction Using Sleep and Health Data
# BSAN6070 — Introduction to Machine Learning | LMU | May 12, 2026

---

## Game Plan

- **Context:** Coursework — BSAN6070, LMU, Spring 2026
- **Business problem:** Identify the top 5 sleep metrics and top 5 health indicators that predict CVD risk. Build an interactive tool where users input their data and receive a personalized CVD risk assessment.
- **Dataset:**
  - X: Dataset/shhs1-dataset-0.13.0.csv
  - Y: Dataset/shhs-cvd-summary-dataset-0.13.0.csv
  - Join key: nsrrid
- **Models:** Logistic Regression, Random Forest, Gradient Boosting
- **Success criteria:** AUC > 0.75 (target 0.80), Recall > 0.70
- **Target variable:** any_cvd (0 = no CVD 76%, 1 = CVD 24%)
- **Deadline:** May 12, 2026
- **Output:** Jupyter Notebook + model.pkl + predict.py + Streamlit app

---

## Code Style Rules

- Write for an intermediate Python developer — clear, readable, not over-engineered
- Lambda, list comprehension, and class are allowed — always add one comment explaining what it does
- Variable names must be descriptive plain English
- Every key decision must have an inline comment explaining WHY
- No overly complex one-liners that require expertise to read
- Style reference: straightforward procedural code — define model, fit, predict, print. No unnecessary abstraction.

---

## Writing Style Rules

- Read Skills_Library/writing-coach.md before writing ANY markdown cell
- Short sentences. One idea per sentence. Max 20 words.
- No AI tone. Never start with "In this step we will leverage..." or "This section aims to..."
- Results cell: state what was found. Never write "the results show that..."
- Never use the words: stop, wait, pause, confirm, or any instruction directed at Jerry
- The notebook is published on GitHub — every cell must read as clean academic writing
- Performance Criteria and Three Models sections must be thorough but follow the same plain English rules

---

## Notebook Structure

### Opening Cells (placed before Part 1, no code)

Cell 1 — Title
# CVD Risk Prediction Using Sleep and Health Data
Author, course, institution, date

Cell 2 — Project Summary
One paragraph. What this project is, what data it uses, what it produces.

Cell 3 — Business Problem
Why CVD prediction matters. Social impact. Two research questions:
- Primary: Can we predict CVD risk using sleep patterns and baseline health data?
- Secondary: What are the top 5 sleep metrics and top 5 health indicators that most influence CVD risk?

Cell 4 — Execution Steps
Numbered list of all 15 steps with one-line description each.

---

### Full Notebook Layout

## Part 1 — Data Preparation  (collapsible)
   ### Step 1 — Data Loading and Merging  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 2 — Quick Scan and Feature Selection  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 3 — Data Cleaning  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 4 — Rename Features  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 5 — EDA  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 6 — Save Modeling Dataset  (collapsible)
      #### Summary
      [code]
      #### Results

## Part 2 — Modeling  (collapsible)
   ### Step 7 — Preprocessing  (collapsible)
      #### Summary  (includes Performance Criteria + Three Models + Why + Preprocessing explanation)
      [code]
      #### Results

   ### Step 8 — Model Assumption Validation  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 9 — K Selection and Model Training  (collapsible)
      #### Summary
      #### Phase 1 — K Selection
      [code]
      #### Phase 1 Results
      #### Phase 2 — Parallel Training
      [code]
      #### Phase 2 Results
      #### Overall Results

   ### Step 10 — Evaluation  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 11 — SHAP Interpretation  (collapsible)
      #### Summary
      [code]
      #### Results

   ### Step 12 — Final Model Selection  (collapsible)
      #### Summary
      [code]
      #### Results

## Part 3 — Results  (collapsible)
   ### Step 13 — Results, Limitations and Conclusion  (collapsible)
      #### Summary
      [code]
      #### Results
         - Best model AUC and Recall
         - Top 5 sleep metrics (from SHAP)
         - Top 5 health indicators (from SHAP)
         - Limitations
         - Future work
         - Recommendations
         - Conclusion

## Part 4 — Deployment  (collapsible)
   ### Step 14 — Deployment  (collapsible)
      #### Summary
      #### Code Part 1 — Save Model
      #### Code Part 2 — predict.py
      #### Code Part 3 — Streamlit App
      #### Code Part 4 — README and requirements.txt
      #### Code Part 5 — GitHub Upload
      #### Results

---

## Skill Library

Read the relevant skill file BEFORE executing each step.
Skills are located at: Skills_Library/

Step 1-6    -> data-prep.md, python.md
Step 5      -> eda.md
Step 7-12   -> ml-engineer.md, modeling.md, model-evaluation.md
Step 11     -> ml-engineer.md (SHAP section)
Step 13     -> storytelling.md
Step 14     -> github-organizer.md
All steps   -> report.md, writing-coach.md, python.md

---

## Execution Instructions

### AUTOMATIC: Steps 1 to 6

---

### Step 1 — Data Loading and Merging

1. Import pandas, numpy, matplotlib, seaborn
2. Load shhs1 CSV into df_shhs1
3. Load shhs-cvd-summary CSV into df_cvd
4. Print shape of each file before merging
5. Inner join on nsrrid into df_merged
   # inner join — only keep patients who exist in both files
6. Print shape after merge — expect ~5,802 rows
7. Print first 5 rows to visually confirm merge

---

### Step 2 — Quick Scan and Feature Selection

1. Calculate missing value percentage for ALL columns
2. Remove identifier columns: nsrrid, pptid
3. Remove date columns: any column containing date or dt in name
4. Remove all CVD outcome columns EXCEPT any_cvd:
   [vital, prev_mi, prev_mip, prev_stk, mi, mip, mi_fatal, stroke,
    stk_fatal, chd_death, cvd_death, angina, revasc_proc, ptca, cabg,
    chf, prev_chf, any_chd, prev_revpro, mi_death, prev_ang, censdate,
    mi_date, mip_date, stk_date, chd_dthdt, cvd_dthdt, ang_date,
    revpro_date, ptca_date, cabg_date, chf_date, afibprevalent, afibincident]
   # removing these prevents data leakage — they are outcomes, not predictors

5. Keep ONLY these 18 features plus any_cvd:

   SLEEP METRICS (5):
   ahi_a0h3, avgsat, minsat, slpeffp, waso

   BODY MEASUREMENTS (2):
   bmi_s1, waist

   CARDIOVASCULAR BASELINE (4):
   systbp, diasbp, chol, hdl

   MEDICAL HISTORY (3):
   srhype, parrptdiab, htnmed1

   DEMOGRAPHICS (4):
   age_s1, gender, race, smokstat_s1

   TARGET:
   any_cvd

6. Print final column list and shape

---

### Step 3 — Data Cleaning

1. Drop columns where missing pct > 50%
   # too much missing data to impute reliably
2. For numeric columns: impute missing with median
   # median is robust to outliers unlike mean
3. For categorical columns: impute missing with mode
4. Remove duplicate rows
5. IQR outlier capping on numeric columns:
   lower = Q1 - 1.5 * IQR
   upper = Q3 + 1.5 * IQR
   Cap at bounds — do NOT remove rows
   # capping preserves sample size while reducing extreme influence
6. Print shape before and after

---

### Step 4 — Rename Features

Apply this rename mapping:

rename_map = {
    ahi_a0h3:    apnea_events_per_hour,
    avgsat:      avg_oxygen_saturation,
    minsat:      min_oxygen_saturation,
    slpeffp:     sleep_efficiency_pct,
    waso:        wake_after_sleep_onset_min,
    bmi_s1:      bmi,
    waist:       waist_circumference_cm,
    systbp:      systolic_bp,
    diasbp:      diastolic_bp,
    chol:        cholesterol,
    hdl:         hdl_cholesterol,
    srhype:      self_reported_hypertension,
    parrptdiab:  history_of_diabetes,
    htnmed1:     taking_bp_medication,
    age_s1:      age_at_baseline,
    gender:      sex,
    race:        race,
    smokstat_s1: smoking_status,
    any_cvd:     any_cvd
}

Print before and after column names to confirm.

---

### Step 5 — EDA

1. Print any_cvd value counts and class balance percentage
2. For each numeric variable: histogram with KDE
   Title must state a conclusion, not just the variable name
3. T-test for each numeric variable: CVD=1 vs CVD=0
   Print p-value and whether difference is statistically significant
4. Male vs Female CVD risk analysis:
   - CVD rate by sex
   - Boxplot comparison
5. Correlation matrix heatmap — flag correlations > 0.8
6. CVD vs non-CVD boxplots for key variables
7. Save all charts to Output/eda_charts/

---

### Step 6 — Save Modeling Dataset

Save df_model to Dataset/dataset_ready_for_modeling.csv
# saving here preserves the clean dataset before any modeling begins
# allows reloading without re-running Steps 1-5
Print confirmation with shape and file path.

---

## SEMI-AUTOMATIC: Steps 7 to 14

For every step:
- Claude Code runs the code automatically
- After code runs, ask Jerry in chat how to proceed
- Wait for Jerry's decision before writing the Results cell
- If Jerry changes any value or model in chat, re-run the affected code and automatically update the Results cell

---

### Step 7 — Preprocessing

Summary cell must include:

PERFORMANCE CRITERIA:
We use two metrics to evaluate model performance.

Recall > 0.70
In medicine, missing a CVD patient is more dangerous than a false alarm.
A missed patient gets no treatment. That can be fatal.
Recall measures how many true CVD cases the model correctly identifies.
We set 0.70 as the minimum — meaning we catch at least 7 out of 10 CVD patients.
Why 0.70 and not higher? Pushing Recall above 0.80 forces the threshold so low
that Precision drops below 0.40 — more than half the flagged patients are healthy.
That creates unnecessary medical interventions. 0.70 is the practical balance point.

AUC > 0.75 (target 0.80)
AUC measures overall discrimination — can the model rank CVD patients above non-CVD patients?
Existing SHHS papers achieve 0.75 to 0.84. We target 0.80 to match the best published results.

Baseline: must beat a model that always predicts No CVD (76% accuracy).

THREE MODELS:
Logistic Regression — baseline model. Simple and interpretable.
If it performs well, the CVD relationship is largely linear.
Easiest to explain to a doctor.

Random Forest — handles non-linear relationships.
CVD risk is not just additive — age plus blood pressure plus poor sleep
together is worse than the sum of parts. Random Forest captures this.
Also robust to outliers and missing values common in medical data.

Gradient Boosting — each tree corrects the errors of the last.
Expected to produce the strongest predictions.
Chosen over XGBoost because it is the original standard implementation
of gradient boosting — more academically defensible than a speed-optimized variant.

Preprocessing code:
1. Separate X and y:
   X = df_model without any_cvd column
   y = df_model any_cvd column

2. Identify column types:
   categorical_cols = [sex, race, smoking_status, self_reported_hypertension,
                       history_of_diabetes, taking_bp_medication]
   numeric_cols = all remaining columns except target

3. Build preprocessing pipeline:
   Numeric: SimpleImputer(median) then StandardScaler
   # StandardScaler fit on train only — prevents data leakage
   Categorical: SimpleImputer(most_frequent) then OneHotEncoder(handle_unknown=ignore)
   # OneHotEncoder converts categories to 0/1 so models can process them

4. Train/Test Split: 80/20, stratified on any_cvd
   # stratify ensures both sets have same 76/24 CVD ratio

5. Undersampling on TRAINING SET ONLY:
   Keep all CVD=1 rows (957 rows)
   Random sample CVD=0 to match (957 rows)
   Combined training set: 1,914 rows, balanced 50/50
   # undersampling directly addresses class imbalance and improves Recall
   # test set remains original distribution — do not undersample test set

6. Print shapes and class balance confirmation for both train and test

---

### Step 8 — Model Assumption Validation

1. Logistic Regression: VIF check
   Calculate VIF for all numeric features
   # VIF measures multicollinearity — Logistic Regression assumes features are not highly correlated
   Flag features with VIF > 10
   Print VIF table sorted descending

2. All models: data leakage check
   Confirm no feature is derived from CVD outcomes
   Print confirmation

3. Class balance check on training set
   Print y_train value counts after undersampling
   Confirm 50/50 split

---

### Step 9 — K Selection and Model Training

PHASE 1 — K SELECTION:

For each model independently test K = 3, 5, 10:
1. Use default parameters for each model
2. Run StratifiedKFold cross-validation for each K
3. Record AUC mean and std for each K
4. Print results table:
   Model | K=3 Mean | K=3 Std | K=5 Mean | K=5 Std | K=10 Mean | K=10 Std

Decision rule:
- Choose K with lowest std (most stable)
- If std difference < 0.01 between two K values, choose higher mean AUC
- If unclear, flag for Jerry to decide in chat

Ask Jerry in chat to confirm K values before Phase 2 begins.

PHASE 2 — PARALLEL TRAINING:

Use K values confirmed by Jerry.

Param grids:

Logistic Regression:
C: [0.01, 0.1, 1, 10, 100]
penalty: [l1, l2]
solver: [liblinear, saga]

Random Forest:
n_estimators: [100, 300, 500]
max_depth: [3, 5, 10, None]
min_samples_split: [2, 5, 10]

Gradient Boosting:
n_estimators: [100, 300, 500]
learning_rate: [0.01, 0.05, 0.1]
max_depth: [3, 5, 6]
subsample: [0.7, 0.8, 1.0]
min_samples_split: [2, 5, 10]

For each model dynamically calculate n_iter:
search_space = product of all param option counts
n_iter = max(10, int(search_space * 0.10))
# cover at least 10% of search space, minimum 10 iterations

Run three models in parallel using joblib.Parallel
Each model: RandomizedSearchCV, scoring=roc_auc
Save: best_estimator, best_auc, best_params, training_time

Orchestrator prints comparison table:
Model | Best AUC | Recall | F1 | Best Params | Training Time

If top two models have AUC difference < 0.02, flag in chat for Jerry to review.

Ask Jerry in chat to confirm results before writing Overall Results.

---

### Step 10 — Evaluation

1. For each model evaluate on X_test:
   - AUC-ROC curve — all three models on one chart
   - Confusion Matrix — one per model
   - Classification Report — Precision, Recall, F1
   - Decision threshold tuning: find threshold that maximizes Recall >= 0.70
     # default 0.5 threshold is rarely optimal for imbalanced medical data
   - Subgroup analysis:
     Age < 65 vs >= 65
     Male vs Female
     Flag any subgroup where AUC drops more than 10% below overall AUC

2. Performance Criteria Pass/Fail table:
   Model | AUC | AUC Pass | Recall | Recall Pass | Beats Baseline

3. Save all charts to Output/evaluation_charts/

Ask Jerry in chat to confirm results before writing Results cell.

---

### Step 11 — SHAP Interpretation

Apply SHAP to the best performing model from Step 10.

1. Global feature importance:
   TreeExplainer for Random Forest and Gradient Boosting
   LinearExplainer for Logistic Regression
   Generate beeswarm summary plot
   Save to Output/shap_summary.png

2. Feature importance table:
   Calculate mean absolute SHAP value per feature
   Print all features ranked by importance
   Separate into two groups:
   - Sleep metrics: apnea_events_per_hour, avg_oxygen_saturation,
     min_oxygen_saturation, sleep_efficiency_pct, wake_after_sleep_onset_min
   - Health indicators: all remaining features
   Print: sleep metrics account for X% of total SHAP importance

3. Identify and save:
   TOP_5_SLEEP = top 5 sleep metrics by mean absolute SHAP
   TOP_5_HEALTH = top 5 health indicators by mean absolute SHAP
   Save both lists to Output/shap_top_features.json
   # Step 14 Streamlit app will read this file to build input fields

4. Waterfall plots for three patients:
   - Highest CVD risk
   - Lowest CVD risk
   - Borderline case
   Save to Output/shap_waterfall/

5. Sanity check:
   age_at_baseline: higher age should mean higher CVD risk (positive SHAP expected)
   systolic_bp: higher BP should mean higher CVD risk (positive SHAP expected)
   sleep_efficiency_pct: lower efficiency should mean higher CVD risk (negative SHAP expected)
   Flag any reversed direction as potential data issue

Ask Jerry in chat to confirm results before writing Results cell.

---

### Step 12 — Final Model Selection

1. Load results from Steps 9, 10, 11
2. Build comparison table:
   Model | AUC | Recall | AUC Pass | Recall Pass | SHAP interpretable | Training Time
3. Apply selection criteria:
   Primary: highest Recall among models that pass AUC > 0.75
   Secondary: interpretability and training speed if metrics are close
4. Write markdown cell with comparison table and decision rationale

Ask Jerry in chat to confirm final model selection before writing Results cell.

---

### Step 13 — Results, Limitations and Conclusion

Results cell must include:
- Best model name, AUC, Recall, decision threshold used
- Top 5 sleep metrics (from SHAP) with SHAP values
- Top 5 health indicators (from SHAP) with SHAP values
- Sleep metrics % contribution vs health indicators % contribution
- Subgroup findings (age, sex)
- Limitations:
  - Data from 1995-1998 — may not reflect modern patients
  - 5,804 patients limits subgroup reliability
  - Model performs poorly on Age < 65 subgroup
  - Not for clinical diagnosis
- Future work:
  - Validate on more recent datasets
  - Test LightGBM and CatBoost
  - Survival analysis for time-to-event prediction
- Recommendations for doctors and healthcare systems
- Conclusion: one paragraph summarizing the key finding

Ask Jerry in chat to confirm before writing Results cell.

---

### Step 14 — Deployment

PART 1 — Save Model
1. Save best model pipeline as Output/model.pkl using joblib.dump
2. Test reload: load model back and run one prediction
3. Print confirmation

PART 2 — predict.py
Create src/predict.py:
- Accept --input argument pointing to a CSV file
- Load Output/model.pkl
- Read input CSV
- Run predict and predict_proba
- Print: prediction (CVD Risk Detected or No CVD Risk) and probability as percentage
- Include usage instructions in docstring at top of file

Also create Dataset/sample_input_patient.csv:
- One row with all 18 feature columns filled with realistic values
- Used for live demo on presentation day

PART 3 — Streamlit App
Create src/app.py:
- Read Output/shap_top_features.json to get TOP_5_SLEEP and TOP_5_HEALTH
  # input fields are determined by SHAP results, not hardcoded
- Two-column input form:
  Left column: Top 5 Sleep Metrics
  Right column: Top 5 Health Indicators
- Large Predict CVD Risk button
- Output section (appears after prediction):
  - Risk level badge: HIGH RISK / MEDIUM RISK / LOW RISK
  - CVD probability percentage
  - One sentence explanation
  - Two-column SHAP bar chart:
    Left: sleep metrics contributions
    Right: health indicators contributions
  - Disclaimer: for educational purposes only, not a substitute for medical advice
- Load Output/model.pkl on app startup

PART 4 — README and requirements.txt
Generate README.md covering:
- Project objective
- Dataset description
- Research questions
- Methods and steps
- Key findings (fill after results)
- Best model performance
- How to run:
  pip install -r requirements.txt
  jupyter notebook notebooks/
  python src/predict.py --input Dataset/sample_input_patient.csv
  streamlit run src/app.py
- Project folder structure
- SHHS dataset citation
- Author, course, institution

Generate requirements.txt with all libraries used.

PART 5 — GitHub Upload
1. git init
2. Create .gitignore:
   Exclude: Dataset/*.csv (too large), __pycache__, .ipynb_checkpoints
   Include: Output/model.pkl, Output/shap_top_features.json, src/app.py, requirements.txt
3. git add .
4. git commit -m "CVD Risk Prediction — BSAN6070 Final Project"
5. Ask Jerry for GitHub repo URL in chat
6. git remote add origin [URL]
7. git push -u origin main
8. Print: GitHub upload complete. Connect src/app.py to Streamlit Cloud to deploy.

Ask Jerry in chat to confirm deployment is complete.

---

## Decision Rules

If missing values > 20% in any column: flag as HIGH RISK in EDA
If missing values > 50% in any column: drop column automatically
If K selection std difference < 0.01: choose higher mean AUC
If K selection unclear: flag for Jerry to decide in chat
If AUC difference < 0.02 between top two models: flag for Jerry to review
If model AUC < 0.75 after tuning: adjust param grid and re-run Phase 2
If model Recall < 0.70: adjust decision threshold using Precision-Recall curve
If any model performs abnormally poorly: flag and check Step 8 assumption validation
If SHAP feature direction contradicts medical expectation: flag as potential data issue
If data leakage detected: stop immediately and alert Jerry in chat
If Jerry changes any value or model in chat: re-run affected step and update Results cell automatically

---

## Tools and Environment

- Language: Python
- Output format: Jupyter Notebook (.ipynb)
- Key libraries: pandas, numpy, matplotlib, seaborn, sklearn, joblib, shap, streamlit, scipy
- Input data: Dataset/
- Output directory: Output/
- Source code: src/
- Notebook: notebooks/
- Streamlit main file: src/app.py
- GitHub: push all files except large CSV files

---

## Project Folder Structure

Project Template/
├── CLAUDE.md
├── README.md
├── requirements.txt
├── Dataset/
│   ├── shhs1-dataset-0.13.0.csv
│   ├── shhs-cvd-summary-dataset-0.13.0.csv
│   ├── dataset_ready_for_modeling.csv
│   └── sample_input_patient.csv
├── notebooks/
│   └── cvd_risk_prediction.ipynb
├── Output/
│   ├── model.pkl
│   ├── shap_top_features.json
│   ├── eda_charts/
│   ├── evaluation_charts/
│   └── shap_waterfall/
├── src/
│   ├── app.py
│   └── predict.py
└── Skills_Library/
    ├── data-prep.md
    ├── eda.md
    ├── ml-engineer.md
    ├── modeling.md
    ├── model-evaluation.md
    ├── python.md
    ├── report.md
    ├── storytelling.md
    ├── github-organizer.md
    └── writing-coach.md

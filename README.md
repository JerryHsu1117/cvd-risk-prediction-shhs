# CVD Risk Prediction Using Sleep and Health Data

**Author:** Jerry Hsu  
**Course:** BSAN6070 — Introduction to Machine Learning  
**Institution:** Loyola Marymount University  
**Date:** May 12, 2026  

---

## Project Objective

Identify the top sleep metrics and health indicators that predict cardiovascular disease (CVD) risk. Build an interactive tool where users input their data and receive a personalized CVD risk assessment.

---

## Research Questions

**Primary:** Can we predict CVD risk using sleep patterns and baseline health data?

**Secondary:** What are the top 5 sleep metrics and top 5 health indicators that most influence CVD risk?

---

## Dataset

- **Source:** Sleep Heart Health Study (SHHS) v0.13.0
- **Files:** `shhs1-dataset-0.13.0.csv` (polysomnography) + `shhs-cvd-summary-dataset-0.13.0.csv` (outcomes)
- **Join key:** `nsrrid`
- **Sample size:** 5,042 patients after cleaning
- **Class balance:** CVD = 0: 76.3%, CVD = 1: 23.7%
- **Time period:** 1995–1998

---

## Methods

Three models were trained and compared: Logistic Regression, Random Forest, and Gradient Boosting.

Preprocessing: StandardScaler for numeric features, OneHotEncoder for categorical features, SimpleImputer for missing values. Training set was undersampled to 50/50 class balance. Test set remained at the original 76/24 distribution.

Hyperparameter tuning: RandomizedSearchCV with StratifiedKFold (K=3 for LR and RF, K=5 for GB). Decision threshold tuned to achieve Recall ≥ 0.70.

Feature importance: SHAP LinearExplainer on the best model.

---

## Key Findings

- **Best model:** Logistic Regression — AUC = 0.8018, Recall = 0.7071, threshold = 0.55
- **Top sleep metric:** Apnea events per hour (AHI) — SHAP = 0.0886
- **Top health indicator:** Age at baseline — SHAP = 0.7336
- **Sleep vs health:** Sleep metrics contribute 5.6% of total SHAP importance; health indicators contribute 94.4%
- **Subgroup note:** Recall for patients under 65 drops to 24.5% — the model is most reliable for older patients

---

## How to Run

**Install dependencies:**
```bash
pip install -r requirements.txt
```

**Run the notebook:**
```bash
jupyter notebook notebooks/cvd_risk_prediction.ipynb
```

**Predict for a single patient (CLI):**
```bash
python src/predict.py --input Dataset/sample_input_patient.csv
```

**Launch the Streamlit app:**
```bash
streamlit run src/app.py
```

---

## Project Structure

```
├── CLAUDE.md                          Project specification
├── README.md                          This file
├── requirements.txt                   Python dependencies
├── Dataset/
│   ├── shhs1-dataset-0.13.0.csv       Raw polysomnography data (not in repo)
│   ├── shhs-cvd-summary-dataset-0.13.0.csv  CVD outcomes (not in repo)
│   ├── dataset_ready_for_modeling.csv Clean modeling dataset
│   └── sample_input_patient.csv       Demo patient for predict.py
├── notebooks/
│   └── cvd_risk_prediction.ipynb      Main analysis notebook
├── Output/
│   ├── model.pkl                      Trained Logistic Regression pipeline
│   ├── shap_top_features.json         SHAP feature rankings
│   ├── phase2_results.json            Model comparison results
│   ├── eda_charts/                    EDA visualizations
│   ├── evaluation_charts/             ROC curves, confusion matrices
│   └── shap_waterfall/                SHAP waterfall plots
├── src/
│   ├── app.py                         Streamlit web app
│   └── predict.py                     CLI prediction script
└── Skills_Library/                    Claude Code skill files
```

---

## Dataset Citation

Zhang, G.-Q., Cui, L., Mueller, R., Tao, S., Kim, M., Rueschman, M., Mariani, S., Mobley, D., & Redline, S. (2018). The National Sleep Research Resource. *Sleep*, 41(10). https://doi.org/10.1093/sleep/zsy217

Dean, D. A., Goldberger, A. L., Mueller, R., Kim, M., Rueschman, M., Mobley, D., Sahoo, S. S., Jayapandian, C. P., Cui, L., Morrical, M. G., Surovec, S., Zhang, G.-Q., & Redline, S. (2016). Scaling Up Scientific Discovery in Sleep Medicine: The National Sleep Research Resource. *Sleep*, 39(5), 1151–1164. https://doi.org/10.5665/sleep.5774

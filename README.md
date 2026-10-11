<div align="center">

# 📈 ChurnLens — Customer Churn Intelligence Platform

**Load customer data → predict who will leave → see exactly why → decide who to save first**

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-189AB4?style=for-the-badge)
![LightGBM](https://img.shields.io/badge/LightGBM-2E8B57?style=for-the-badge)
![SHAP](https://img.shields.io/badge/SHAP-8A2BE2?style=for-the-badge)
![SQL](https://img.shields.io/badge/SQL-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Runs Locally](https://img.shields.io/badge/Runs-Locally-brightgreen?style=for-the-badge)

</div>

---

## 📌 Overview

An end-to-end **customer churn prediction** project: it extracts data with SQL, cleans it, explores it, engineers features, compares several models with cross-validation, tunes the best ones, and explains every prediction with **SHAP** in an interactive, animated Streamlit dashboard.

Knowing *that* a customer might churn is not enough. Teams need to know **why**, and **who is worth saving first**. ChurnLens gives each customer a risk score, the exact reasons behind it, suggested retention actions, and an ROI planner that sizes the best campaign.

It runs fully on your machine. Try the built-in **demo dataset** in one click, or upload **your own CSV / Excel file**.

## ✨ Features

- 🗄️ **SQL extraction:** a demo warehouse of 5 related tables is joined into one row per customer with window functions and conditional aggregates
- 🧹 **Automatic cleaning** with a full report of every fix: duplicates, typos, missing tokens, impossible values, outliers, text-numbers
- 📊 **EDA tab:** churn by segment, driver curves, distributions by outcome, correlation map, missing values
- 🛠️ **Feature engineering** inside the model pipeline (usage trends, per-seat ratios, billing and support signals), so nothing leaks between CV folds
- 🏁 **5 models compared** with stratified cross-validation: Logistic Regression, Random Forest, Gradient Boosting, **XGBoost**, **LightGBM**
- 🎛️ **Hyper-parameter tuning** with randomized search on the top models; the winner is picked by PR-AUC (or ROC-AUC / F1)
- 🧪 **Honest evaluation:** one-shot hold-out test, ROC / PR curves, calibration, gains chart, confusion matrix and an interactive threshold slider
- 🔍 **SHAP explanations:** global importance, beeswarm, dependence plots, and a per-customer waterfall with plain-English reasons
- 🧮 **What-if simulator:** change a customer's attributes and watch their churn risk and drivers update live
- 💰 **ROI planner:** set offer cost and save rate, get the best campaign size and a ranked call list; download all risk scores as CSV
- 📂 **Custom dataset upload** (CSV, TSV, Excel, JSON, Parquet) with auto-detected churn, ID and revenue columns, plus optional SQL over your file
- 🎨 Smooth dark glass UI: counting KPIs, animated risk gauge, staggered reveals, ambient motion (respects *reduced motion*)

## 🛠️ Tech Stack

| Area | Tools |
|---|---|
| Language | Python |
| Data | Pandas, NumPy, SQL (SQLite) |
| Machine learning | scikit-learn, XGBoost, LightGBM |
| Explainability | SHAP |
| Dashboard | Streamlit, Plotly, custom CSS animations |
| Testing | pytest, Streamlit `AppTest` |

## 🔄 How It Works

```
SQL tables → Extract → Clean → Engineer features → Cross-validate 5 models
                                                          │
                                           Tune the top models (random search)
                                                          │
                              Hold-out test → SHAP → Risk scores → Actions + ROI
```

1. **Extract:** `sql/extract_features.sql` builds one row per customer, de-duplicating with `ROW_NUMBER()` and splitting usage into recent vs prior months.
2. **Clean:** every automatic fix is logged and shown in the *Data & SQL* tab.
3. **Compare and tune:** all models run through the same leak-safe pipeline (imputation, scaling and encoding are fitted per fold).
4. **Evaluate:** the winner is scored once on customers it never saw during CV or tuning.
5. **Explain and act:** SHAP values drive the reasons, the what-if simulator and the retention suggestions.

---

## 🚀 Getting Started

> **Requirements:** Python 3.10+ and `git`. No GPU needed.

**1. Clone the repository and open the project folder**

```bash
git clone https://github.com/<your-username>/churnlens.git
cd churnlens
```

**2. Create and activate a virtual environment**

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# Mac / Linux
source .venv/bin/activate
```

**3. Install the dependencies**

```bash
pip install -r requirements.txt
```

**4. Run the dashboard**

```bash
streamlit run app.py
```

Your browser opens at `http://localhost:8501`. Choose **Demo dataset** in the sidebar and press **Run analysis**.
(In VS Code you can also press **F5** and pick *ChurnLens: Streamlit dashboard*, or double-click `run.bat` / run `./run.sh`.)

| Compute budget | Time on 2 CPU cores (demo data) | What it does |
|---|---|---|
| Quick *(default)* | ~40 seconds | 3-fold CV, tunes the best model |
| Balanced | ~5 minutes | 5-fold CV, tunes the top 2 models |
| Thorough | longer | more tuning trials on the top 3 |

Results are cached in `artifacts/cache/`, so reopening is instant.

---

## 📂 Use Your Own Dataset

Pick **Upload my own** in the sidebar and drop in a file. ChurnLens guesses the churn column and the value that means "churned" (for example `Churn = Yes`), the customer ID and a revenue column. Change any guess with the dropdowns.

- Needs at least **150 rows**, with **30+ churned** and **30+ retained** customers
- Everything else is cleaned and encoded automatically
- Optional **SQL step:** your file is available as table `data`
- Try `data/sample_custom_dataset.csv` (target `Churn`, positive `Yes`, ID `customerID`, revenue `MonthlyCharges`). The classic IBM Telco churn CSV works with the same settings.

Command-line equivalent:

```bash
python -m src.pipeline --csv my.csv --target Churn --positive Yes --id customerID --preset Balanced
```

## 📁 Project Structure

```
churnlens/
├─ app.py                  # Streamlit dashboard (8 tabs)
├─ run.bat / run.sh        # one-click launchers
├─ requirements.txt
├─ sql/
│  ├─ schema.sql           # demo warehouse schema (5 tables)
│  └─ extract_features.sql # one-row-per-customer extraction query
├─ src/
│  ├─ data_gen.py          # synthetic demo data (with injected mess)
│  ├─ db.py                # SQL extraction + read-only SQL console
│  ├─ loader.py            # file reading, target / ID / revenue detection
│  ├─ cleaning.py          # reported, leak-safe cleaning
│  ├─ features.py          # feature engineering + preprocessing pipeline
│  ├─ modeling.py          # model zoo, CV, tuning, thresholds, metrics
│  ├─ explain.py           # SHAP helpers
│  ├─ insights.py          # labels, reasons, retention plays, ROI curve
│  ├─ pipeline.py          # orchestration, caching, CLI
│  └─ ui.py, charts.py     # animated theme, components, Plotly figures
├─ tests/                  # pytest + Streamlit AppTest smoke test
└─ data/
   ├─ demo_churn.db        # demo SQLite warehouse
   └─ sample_custom_dataset.csv
```

## 🗃️ Demo Dataset

10,000 **synthetic** SaaS customers across five tables: `customers`, `subscriptions`, `monthly_usage`, `support_tickets` and `payments`. It is generated with known churn drivers (contract type, usage momentum, support friction, payment failures, renewal timing, tenure) so SHAP has real patterns to find. It also contains deliberate real-world mess so the cleaning stage has work to do.

On this data, the models reach roughly **0.85 ROC-AUC** and **0.68–0.70 PR-AUC** in testing. These numbers describe the simulated world, not a real business.

## ⚠️ Important Notes

- **Models are close on the demo data.** The five models score within about 0.01 PR-AUC of each other, and the winner can change between runs. The value here is the process and the explanations, not a big gap between models.
- **Correlation is not causation.** SHAP shows what drives the *model's* prediction; confirm with experiments before acting on a driver.
- **Calendar dates are not used as features.** Combined with tenure they can reconstruct the churn date and leak the label.
- **Large files.** Above 40,000 training rows, comparison and tuning run on a stratified sample.
- **Privacy.** Everything runs locally; your data is never uploaded anywhere.

## 🧪 Tests

```bash
pytest -q tests
```

## 🔮 Future Improvements

- [ ] Time-based train/test split for datasets with a date column
- [ ] Survival analysis (time-to-churn)
- [ ] Batch scoring of new customers with a saved model
- [ ] Model and SHAP export, plus a REST API
- [ ] Docker image and cloud deployment

## 📄 License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for details.

## 👤 Author

**Vikram Mali**
[Portfolio](https://vikram-mali-portfolio.vercel.app/) • [LinkedIn](https://www.linkedin.com/in/vikram-mali-660a34438) • vikram2k04@gmail.com

<div align="center">

⭐ If you find this project useful, please give it a star!

</div>

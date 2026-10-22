# End-to-End Data & AI Engineering Platform — Project Metrics

> **Central, authoritative record of all verified project metrics.**
> Every value in this document is traced to a source file, saved output, or reproducible execution.
> Metrics that could not be verified are explicitly marked.

---

## 1. Project Overview & Scope

| Property | Value |
|---|---|
| Project Name | End-to-End Data & AI Engineering Platform |
| Repository | [zepto-data-ai-platform](https://github.com/choppabharathkumar/zepto-data-ai-platform) |
| Branch | `main` |
| Modules | 3 (Data Pipeline, Analytics, Support Assistant) |
| Type | AI/ML Capstone Project |

---

## 2. Metric Verification Methodology

Each metric in this document carries one of the following verification statuses:

| Status | Meaning |
|---|---|
| ✅ Verified | Confirmed from a committed output artifact or source code logic |
| 🔁 Reproduced | Obtained by re-running the documented evaluation workflow |
| 📄 Historical / Unverified | Reported in an earlier document but not independently corroborated |
| ❌ Unavailable | Not measured, not found in the repository, or not applicable |

---

## 3. Module 1 — Data Pipeline Results

### 3.1 Scraping & Cleaning

| Metric | Value | Evidence | Status |
|---|---|---|---|
| Books scraped | 69 | `data_pipeline/data/raw/books_raw.csv` (69 data rows) | ✅ Verified |
| Categories covered | 3 | Travel, Mystery, Historical Fiction | ✅ Verified |
| Raw dataset rows | 69 | `data_pipeline/data/raw/books_raw.csv` | ✅ Verified |
| Cleaned dataset rows | 69 | `data_pipeline/data/processed/books_clean.csv` | ✅ Verified |
| Raw columns | 7 | `title, category, price_gbp, price_inr, rating, in_stock, stock_count` | ✅ Verified |
| GBP-to-INR rate | 105.50 | `data_pipeline/constants.py` → `GBP_TO_INR = 105.50` | ✅ Verified |

### 3.2 Database

| Metric | Value | Evidence | Status |
|---|---|---|---|
| SQLite database | `books.db` (28,672 bytes) | `data_pipeline/books.db` | ✅ Verified |
| Tables | 2 (`categories`, `books`) | Schema in `data_pipeline/database_manager.py` | ✅ Verified |
| Primary keys | `category_id`, `book_id` | Schema in `database_manager.py` | ✅ Verified |
| Foreign key | `books.category_id → categories.category_id` | Schema in `database_manager.py` | ✅ Verified |

### 3.3 SQL Queries

| Metric | Value | Evidence | Status |
|---|---|---|---|
| Number of SQL queries | 6 | 6 `.sql` files in `data_pipeline/sql/` | ✅ Verified |
| Query output files | 6 | 6 `.txt` files in `data_pipeline/outputs/query_outputs/` | ✅ Verified |
| SQL/Pandas JOIN match | `True` | `data_pipeline/outputs/pandas_vs_sql_comparison.txt` → "Results match: True" | ✅ Verified |

**SQL Query Index:**

| Query | Clauses | Output File |
|---|---|---|
| `books_in_stock_high_rating` | `SELECT` / `WHERE` | `books_in_stock_high_rating.txt` |
| `ten_most_expensive_books` | `ORDER BY` / `LIMIT` | `ten_most_expensive_books.txt` |
| `distinct_ratings` | `DISTINCT` | `distinct_ratings.txt` |
| `books_in_selected_categories` | `IN`, `JOIN` | `books_in_selected_categories.txt` |
| `midrange_priced_books` | `BETWEEN` | `midrange_priced_books.txt` |
| `top_rated_books_by_category` | `JOIN`, `ORDER BY`, `LIMIT` | `top_rated_books_by_category.txt` |

---

## 4. Module 2 — EDA & Machine Learning Results

### 4.1 Dataset

| Metric | Value | Evidence | Status |
|---|---|---|---|
| Source | `sns.load_dataset("titanic")` | `analytics/eda.py` line 37 | ✅ Verified |
| Offline copy | `analytics/titanic.csv` | File committed | ✅ Verified |
| Shape (raw) | 891 × 15 | `analytics/README.md` | ✅ Verified |
| Shape (clean) | 889 × 14 | After dropping `deck` column and 2 `embarked`-null rows | ✅ Verified |
| Train/test split | 711 / 178 rows | `test_size=0.2, random_state=42, stratify=y` | ✅ Verified |

### 4.2 Classification Results

Source: `analytics/outputs/model_results.csv`

| Model | Accuracy | Precision | Recall | F1 | AUC | Status |
|---|---|---|---|---|---|---|
| Logistic Regression | 0.8090 | 0.7833 | 0.6912 | 0.7344 | 0.8610 | ✅ Verified |
| Decision Tree | 0.8090 | 0.8148 | 0.6471 | 0.7213 | 0.8560 | ✅ Verified |
| **Random Forest** | **0.8202** | **0.7812** | **0.7353** | **0.7576** | **0.8215** | ✅ Verified |

**Notes:**
- All metrics computed on the held-out test set (178 rows, 20% split).
- Preprocessing fitted only on training data via `sklearn.Pipeline` — no data leakage.
- Random Forest is the best classifier by F1 and Accuracy.

### 4.3 Historical Resume Figures — Corroboration

An older resume reported: Accuracy 82.02%, F1 75.76%, ROC-AUC 0.8215.

| Resume Claim | Matched Model | Match? | Evidence |
|---|---|---|---|
| Accuracy = 82.02% | Random Forest (0.8202) | ✅ Yes | `model_results.csv` |
| F1 = 75.76% | Random Forest (0.7576) | ✅ Yes | `model_results.csv` |
| ROC-AUC = 0.8215 | Random Forest (0.8215) | ✅ Yes | `model_results.csv` |

**Conclusion:** All three historical resume figures are corroborated by the committed `model_results.csv` and correspond to the **Random Forest** classifier.

### 4.4 Imbalance Comparison (Logistic Regression)

| Strategy | Precision | Recall | F1 | Status |
|---|---|---|---|---|
| Baseline | 0.7833 | 0.6912 | 0.7344 | ✅ Verified |
| Balanced Weight | 0.7183 | 0.7500 | 0.7338 | ✅ Verified |
| SMOTE | 0.7353 | 0.7353 | 0.7353 | ✅ Verified |

- SMOTE resampled training set: 711 → 878 rows.
- `class_weight="balanced"` achieved the highest Recall (0.7500).

Source: `analytics/README.md`, `analytics/modeling.py`

### 4.5 GridSearchCV Tuning (Random Forest)

| Metric | Value | Evidence | Status |
|---|---|---|---|
| Parameter grid | `n_estimators: [100, 200], max_depth: [5, 10, None], max_features: ["sqrt", "log2"]` | `modeling.py` lines 340–344 | ✅ Verified |
| Best parameters | `n_estimators=100, max_depth=5, max_features="sqrt"` | `analytics/README.md` | ✅ Verified |
| Best CV F1 score | 0.7420 | `analytics/README.md` | ✅ Verified |
| OOB Score | 0.8087 | `analytics/README.md` | ✅ Verified |

### 4.6 Regression — Fare Prediction (Linear Regression)

Source: `analytics/outputs/regression_results.txt`

| Metric | Value | Status |
|---|---|---|
| MAE | 24.4711 | ✅ Verified |
| RMSE | 60.1040 | ✅ Verified |
| R² | 0.3020 | ✅ Verified |
| Adjusted R² | 0.2548 | ✅ Verified |

**Interpretation:** R² = 0.30 means the features explain ~30% of fare variance. Residual plot shows heteroscedasticity — errors fan out at higher fares. A log-transformation of fare would likely improve this.

### 4.7 EDA Statistical Findings

| Finding | Value | Evidence | Status |
|---|---|---|---|
| Age median (imputed) | 28.0 | `analytics/README.md` | ✅ Verified |
| Age outliers (1.5×IQR) | 65 | `analytics/README.md` | ✅ Verified |
| Fare mean | 32.10 | `analytics/README.md` | ✅ Verified |
| Fare median | 14.45 | `analytics/README.md` | ✅ Verified |
| Fare mode | 8.05 | `analytics/README.md` | ✅ Verified |
| Fare skewness | 4.80 | `analytics/README.md` | ✅ Verified |
| Fare outliers (1.5×IQR) | 114 | `analytics/README.md` | ✅ Verified |
| Fare distribution | Right-skewed (positive) | Mean > Median > Mode | ✅ Verified |
| Female survival rate | 74.0% | `analytics/README.md` | ✅ Verified |
| Male survival rate | 18.9% | `analytics/README.md` | ✅ Verified |
| Strongest correlation | fare ↔ pclass = −0.5482 | `analytics/README.md` | ✅ Verified |

### 4.8 Visualisations

13 charts saved to `analytics/outputs/charts/`:

| Chart | File | Status |
|---|---|---|
| Age distribution | `age_distribution.png` | ✅ Present |
| Fare distribution | `fare_distribution.png` | ✅ Present |
| Correlation matrix | `correlation_matrix.png` | ✅ Present |
| Data story 1 — Survival by sex & class | `story1_survival_sex_class.png` | ✅ Present |
| Data story 2 — Age by survival & sex | `story2_age_survival_sex.png` | ✅ Present |
| Data story 3 — Fare by class & survival | `story3_fare_class_survival.png` | ✅ Present |
| Data story 4 — Embarkation & survival | `story4_embarkation_survival.png` | ✅ Present |
| Confusion matrix — Logistic Regression | `cm_logistic_regression.png` | ✅ Present |
| Confusion matrix — Decision Tree | `cm_decision_tree.png` | ✅ Present |
| Confusion matrix — Random Forest | `cm_random_forest.png` | ✅ Present |
| ROC curves | `roc_curves.png` | ✅ Present |
| Decision tree visualisation | `decision_tree.png` | ✅ Present |
| Regression residuals | `regression_residuals.png` | ✅ Present |

### 4.9 Model Artifact

| Artifact | File | Size | Status |
|---|---|---|---|
| Saved sklearn Pipeline (preprocessor + RF) | `analytics/models/best_pipeline.joblib` | 425,243 bytes | ✅ Present |

Reload verified — accepts raw (unprocessed) features and preprocesses automatically.

---

## 5. Module 3 — RAG Support Assistant Results

### 5.1 Ingestion & Retrieval Configuration

| Metric | Value | Evidence | Status |
|---|---|---|---|
| Documents ingested | 8 policy `.txt` files | `support_assistant/docs/` | ✅ Verified |
| Chunks produced | 14 | `support_assistant/README.md` → "14 chunks from 8 documents" | ✅ Verified |
| Chunk size | 500 characters | `ingest.py` | ✅ Verified |
| Chunk overlap | 50 characters | `ingest.py` | ✅ Verified |
| Embedding model | all-MiniLM-L6-v2 (local ONNX) | `support_assistant/README.md` | ✅ Verified |
| Vector store | ChromaDB | `support_assistant/retriever.py` | ✅ Verified |
| Top-k retrieval | 3 | `retriever.py` → `top_k=3` | ✅ Verified |
| Similarity metric | Cosine similarity | `support_assistant/README.md` | ✅ Verified |

### 5.2 LangGraph Architecture

| Component | Value | Evidence | Status |
|---|---|---|---|
| Graph nodes | 3 (`classify_intent`, `retrieve_and_answer`, `direct_answer`) | `agent.py` | ✅ Verified |
| Conditional routing | Yes — intent-based (`policy_question` / `general_question`) | `agent.py` | ✅ Verified |
| Mock mode | Default (`MOCK_LLM=1`) — fully deterministic, no API key | `agent.py` | ✅ Verified |
| Output schema | `AssistantResponse(answer, sources, confidence)` (Pydantic) | `agent.py` | ✅ Verified |

### 5.3 Test Results

| Metric | Value | Evidence | Status |
|---|---|---|---|
| Total tests | 15 | `support_assistant/test_agent.py` | ✅ Verified |
| Test categories | Intent routing (8), general queries (2), schema (3), retrieval correctness (2) | Test file inspection | ✅ Verified |
| Test mode | MOCK_LLM (deterministic, no API key) | `os.environ["MOCK_LLM"] = "1"` in test file | ✅ Verified |

**Test execution status:** Tests require `ingest.py` to have been run first (ChromaDB populated). Tests validate mock-mode behavior only — they do not measure retrieval accuracy or response quality in a production sense.

### 5.4 Metrics Not Measured

| Metric | Status | Reason |
|---|---|---|
| Retrieval accuracy / relevance score | ❌ Unavailable | No ground-truth Q&A evaluation dataset exists |
| Response latency | ❌ Unavailable | Not benchmarked |
| Production throughput | ❌ Unavailable | Not deployed to production |
| LLM-mode quality | ❌ Unavailable | Optional extension, depends on external API key |

### 5.5 Known Limitations

- Mock mode provides deterministic, template-based responses — it does not measure LLM generation quality.
- Passing unit tests validates pipeline plumbing and routing correctness, not retrieval accuracy.
- The 14-chunk corpus is intentionally small (8 short policy documents); retrieval precision is high by construction but would not generalise to larger document sets without further evaluation.

---

## 6. Related Project Metrics (Separate Project)

> **Hybrid Network Intrusion Detection System** — This is a separate project, not part of this repository. The following figures are from an older resume and are recorded here for completeness. They have **not** been independently verified from this repository.

| Metric | Value | Status |
|---|---|---|
| Overall accuracy | 94.12% | 📄 Historical / Unverified |
| Attack recall | 90.85% | 📄 Historical / Unverified |
| PortScan detection rate | 92.1% | 📄 Historical / Unverified |
| Brute Force detection rate | 87.8% | 📄 Historical / Unverified |
| XSS detection rate | 96.9% | 📄 Historical / Unverified |

These figures are from a **different project** and must not be conflated with the metrics of this platform.

---

## 7. Model Comparison Table

### Classification (Titanic Survival Prediction)

| Model | Accuracy | Precision | Recall | F1 | AUC | Best? |
|---|---|---|---|---|---|---|
| Logistic Regression | 0.8090 | 0.7833 | 0.6912 | 0.7344 | 0.8610 | |
| Decision Tree | 0.8090 | 0.8148 | 0.6471 | 0.7213 | 0.8560 | |
| **Random Forest** | **0.8202** | 0.7812 | **0.7353** | **0.7576** | 0.8215 | ✅ |

### Regression (Fare Prediction)

| Model | MAE | RMSE | R² | Adj R² |
|---|---|---|---|---|
| Linear Regression | 24.4711 | 60.1040 | 0.3020 | 0.2548 |

> **Note:** Classification and regression metrics are not directly comparable — accuracy/F1/AUC measure categorical prediction quality, while MAE/RMSE/R² measure continuous prediction error.

---

## 8. Evaluation Configuration & Reproducibility

### Classification

| Parameter | Value |
|---|---|
| Test split | 20% (`test_size=0.2`) |
| Random seed | 42 |
| Stratification | Yes (`stratify=y`) |
| Preprocessing | `ColumnTransformer` inside `sklearn.Pipeline` (fit on train only) |
| Cross-validation | 5-fold (GridSearchCV) |
| Scoring metric (GridSearchCV) | F1 |

### Regression

| Parameter | Value |
|---|---|
| Test split | 20% (`test_size=0.2`) |
| Random seed | 42 |
| Features | `pclass, age, sibsp, parch, sex, embarked` |
| Target | `fare` |

### Data Pipeline

| Parameter | Value |
|---|---|
| Source website | `https://books.toscrape.com/` |
| Minimum categories | 3 |
| Minimum books | 60 |
| Exchange rate | 1 GBP = 105.50 INR (fixed constant) |

### Reproduction Command

```bash
# Module 1
python data_pipeline/main.py

# Module 2
python analytics/main.py

# Module 3
python support_assistant/ingest.py
python support_assistant/test_agent.py
```

---

## 9. Test & Validation Results

| Module | Test / Validation | Status | Evidence |
|---|---|---|---|
| Data Pipeline | Raw CSV row count = 69 | ✅ Pass | `books_raw.csv` |
| Data Pipeline | Cleaned CSV row count = 69 | ✅ Pass | `books_clean.csv` |
| Data Pipeline | Categories = 3 | ✅ Pass | CSV column inspection |
| Data Pipeline | SQLite DB exists with 2 tables | ✅ Pass | `books.db` (28 KB) |
| Data Pipeline | 6 SQL files present | ✅ Pass | `data_pipeline/sql/` |
| Data Pipeline | 6 query output files present | ✅ Pass | `data_pipeline/outputs/query_outputs/` |
| Data Pipeline | SQL JOIN = Pandas merge | ✅ Pass | `pandas_vs_sql_comparison.txt` → "Results match: True" |
| Analytics | 13 chart PNGs present | ✅ Pass | `analytics/outputs/charts/` |
| Analytics | `model_results.csv` present | ✅ Pass | 3 models × 5 metrics |
| Analytics | `regression_results.txt` present | ✅ Pass | MAE, RMSE, R², Adj R² |
| Analytics | `best_pipeline.joblib` present | ✅ Pass | 425 KB |
| Support Assistant | 8 policy documents present | ✅ Pass | `support_assistant/docs/` |
| Support Assistant | 15 test functions present | ✅ Pass | `test_agent.py` |
| Support Assistant | Dockerfile present | ✅ Pass | `support_assistant/Dockerfile` |

---

## 10. Limitations, Missing Metrics & Unverified Claims

| Item | Status | Notes |
|---|---|---|
| Confusion matrix exact values | ❌ Unavailable | Charts exist as PNGs but numeric cell values are not saved to a text/CSV file |
| Support assistant retrieval accuracy | ❌ Unavailable | No ground-truth evaluation dataset; tests validate routing, not quality |
| Support assistant response latency | ❌ Unavailable | Not benchmarked |
| LLM-mode (MOCK_LLM=0) quality | ❌ Unavailable | Optional extension, depends on external API key |
| Network IDS metrics | 📄 Historical | Separate project; not verifiable from this repository |
| Data pipeline re-run row counts | ⚠️ May vary | Live site content may change; committed CSVs capture a specific scrape |

---

## 11. Source Files & Output Artifacts

| Artifact | Path |
|---|---|
| Raw books CSV | `data_pipeline/data/raw/books_raw.csv` |
| Cleaned books CSV | `data_pipeline/data/processed/books_clean.csv` |
| SQLite database | `data_pipeline/books.db` |
| SQL query files (×6) | `data_pipeline/sql/*.sql` |
| Query output files (×6) | `data_pipeline/outputs/query_outputs/*.txt` |
| SQL vs Pandas comparison | `data_pipeline/outputs/pandas_vs_sql_comparison.txt` |
| Titanic dataset | `analytics/titanic.csv` |
| Classification results | `analytics/outputs/model_results.csv` |
| Regression results | `analytics/outputs/regression_results.txt` |
| Charts (×13) | `analytics/outputs/charts/*.png` |
| Saved pipeline | `analytics/models/best_pipeline.joblib` |
| Policy documents (×8) | `support_assistant/docs/doc_01.txt` – `doc_08.txt` |
| Test file | `support_assistant/test_agent.py` |

---

## 12. Final Project Verification Checklist

| Check | Result |
|---|---|
| All 3 modules present and complete | ✅ |
| Root README links to PROJECT_METRICS.md | ✅ |
| Per-module READMEs present | ✅ |
| Project name consistent across all docs | ✅ |
| No credentials or API keys committed | ✅ |
| No fabricated metrics | ✅ |
| Historical claims clearly distinguished | ✅ |
| Unavailable metrics explicitly marked | ✅ |
| Git history preserved | ✅ |
| Repository slug unchanged | ✅ |

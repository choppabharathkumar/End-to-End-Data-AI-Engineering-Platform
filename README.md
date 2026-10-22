# End-to-End Data & AI Engineering Platform

> 📊 **[View all verified project metrics →](PROJECT_METRICS.md)**

## Overview

End-to-End Data & AI Engineering Platform is an end-to-end Artificial Intelligence and Machine Learning capstone project. The repository contains three integrated modules that demonstrate the workflow of a real AI/ML engineer — from collecting raw data to building analytics and deploying an AI-powered support assistant.

| Module | Status | Marks |
| ------- | ------ | ----- |
| Data Pipeline | ✅ Complete | 25 |
| Analytics | ✅ Complete | 50 |
| Support Assistant | ✅ Complete | 25 |

---

## Repository Structure

```
zepto-data-ai-platform/
│
├── data_pipeline/
│   ├── data/
│   │   ├── raw/                  ← raw scraped CSV
│   │   └── processed/            ← cleaned, typed CSV
│   ├── sql/                      ← one .sql file per query
│   ├── outputs/
│   │   └── query_outputs/        ← one .txt file per query result
│   ├── books.db                  ← SQLite database
│   ├── constants.py
│   ├── utils.py
│   ├── scraper.py
│   ├── database_manager.py
│   ├── sql_queries.py
│   ├── main.py
│   ├── requirements.txt
│   └── README.md
│
├── analytics/
│   ├── outputs/
│   │   ├── charts/               ← 13 PNG visualisations
│   │   ├── model_results.csv
│   │   └── regression_results.txt
│   ├── models/
│   │   └── best_pipeline.joblib  ← saved sklearn Pipeline
│   ├── titanic.csv               ← offline dataset copy
│   ├── constants.py
│   ├── eda.py
│   ├── modeling.py
│   ├── main.py
│   ├── requirements.txt
│   └── README.md
│
├── support_assistant/
│   ├── docs/                     ← 8 policy .txt files
│   ├── chroma_db/                ← ChromaDB vector store (generated)
│   ├── agent.py
│   ├── api.py
│   ├── ingest.py
│   ├── retriever.py
│   ├── constants.py
│   ├── test_agent.py
│   ├── Dockerfile
│   ├── requirements.txt
│   └── README.md
│
├── .gitignore
└── README.md
```

---

## Modules

### 1. Data Pipeline

Scrapes book data from [books.toscrape.com](https://books.toscrape.com/),
cleans the data with pandas, stores it in a two-table normalised SQLite
database, and executes 6 SQL queries covering all required clauses.

**Fixed exchange rate:** `1 GBP = 105.50 INR` (project-defined constant,
no live API, no date reference — per the capstone specification).

#### Technologies

- Python · Requests · BeautifulSoup · Pandas · SQLite3

#### Run

```bash
# Install (from repo root)
pip install -r data_pipeline/requirements.txt

# Run
python data_pipeline/main.py
```

See [`data_pipeline/README.md`](data_pipeline/README.md) for the full
7-stage pipeline description, directory layout, SQL query index, and
design decisions.

---

### 2. Analytics

Complete analytics pipeline on the Titanic dataset: data profiling,
missing-value analysis, EDA with 13 charts, a 4-chart data story,
three classifiers (Logistic Regression, Decision Tree, Random Forest),
class-imbalance handling (balanced weights + SMOTE), GridSearchCV tuning,
linear regression for fare prediction, and a saved sklearn Pipeline.

#### Technologies

- Python · Pandas · NumPy · Matplotlib · Seaborn · scikit-learn · imbalanced-learn · joblib

#### Run

```bash
# Install (from repo root)
pip install -r analytics/requirements.txt

# Run
python analytics/main.py
```

See [`analytics/README.md`](analytics/README.md) for the full EDA steps,
ML pipeline description, model comparison table, and output file listing.

---

### 3. Support Assistant

RAG-based customer support assistant grounded in 8 Zepto policy documents.
Uses ChromaDB with all-MiniLM-L6-v2 embeddings for semantic retrieval,
a LangGraph StateGraph (3 nodes + conditional routing) for intent
classification and answer generation, and a FastAPI endpoint for serving
queries. Ships with a Dockerfile and 15 automated tests.

#### Technologies

- Python · FastAPI · LangGraph · ChromaDB · Pydantic · Docker

#### Run

```bash
# Install (from repo root)
pip install -r support_assistant/requirements.txt

# Ingest policy documents into ChromaDB (run once)
python support_assistant/ingest.py

# Start the API server (mock mode — no API key needed)
python -m uvicorn support_assistant.api:app --port 7860

# Run tests
python support_assistant/test_agent.py
```

See [`support_assistant/README.md`](support_assistant/README.md) for the
full RAG pipeline architecture, LangGraph graph description, example call
transcripts, and Docker instructions.

---

## Installation & Setup

> **Requirements approach:** This project uses **per-module `requirements.txt` files** — one inside each module folder. There is no single consolidated root-level requirements file. Install only the module you intend to run.

Clone the repository:

```bash
git clone https://github.com/choppabharathkumar/zepto-data-ai-platform.git
cd zepto-data-ai-platform
```

Install dependencies per module:

```bash
# Module 1 — Data Pipeline
pip install -r data_pipeline/requirements.txt

# Module 2 — Analytics
pip install -r analytics/requirements.txt

# Module 3 — Support Assistant
pip install -r support_assistant/requirements.txt
```

---


## Running the Project

### Data Pipeline

```bash
python data_pipeline/main.py
```

Outputs are written to `data_pipeline/data/`, `data_pipeline/sql/`,
`data_pipeline/outputs/`, and `data_pipeline/books.db`.

### Analytics

```bash
python analytics/main.py
```

Outputs are written to `analytics/outputs/` (charts, CSV) and
`analytics/models/` (saved pipeline).

### Support Assistant

```bash
python support_assistant/ingest.py          # one-time ingestion
python -m uvicorn support_assistant.api:app --port 7860
```

API available at `http://localhost:7860/ask`.

---

## Repository Workflow

Git is managed using a feature branch workflow:

- Feature branch created for each sprint.
- At least two commits per branch.
- Merged back into `main` when the sprint is complete.

---

## Key Results

For the complete, evidence-backed metrics with verification status, see **[PROJECT_METRICS.md](PROJECT_METRICS.md)**.

### Data Pipeline

- **69 books** scraped across **3 categories** (Travel, Mystery, Historical Fiction)
- 2-table normalised SQLite database with PK/FK relationships
- 6 SQL queries covering all required clauses; SQL JOIN vs Pandas merge **match confirmed**

### Analytics & Machine Learning

| Model | Accuracy | F1 | AUC |
|---|---|---|---|
| Logistic Regression | 0.8090 | 0.7344 | 0.8610 |
| Decision Tree | 0.8090 | 0.7213 | 0.8560 |
| **Random Forest** | **0.8202** | **0.7576** | **0.8215** |

- Linear Regression fare prediction: R² = 0.3020, MAE = 24.47
- 13 EDA/ML visualisations, saved sklearn Pipeline (preprocessor + RF)

### RAG Support Assistant

- 8 policy documents → 14 chunks (ChromaDB + all-MiniLM-L6-v2)
- LangGraph StateGraph with 3 nodes and intent-based conditional routing
- 15 automated tests (mock mode, no API key required)

---

## Author

**choppabharathkumar**

Artificial Intelligence and Machine Learning Capstone Project

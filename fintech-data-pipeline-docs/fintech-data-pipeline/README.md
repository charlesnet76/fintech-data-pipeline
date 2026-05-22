# fintech-data-pipeline

End-to-end data engineering pipeline processing FinTech transaction data — from raw CSV ingestion to transformed analytics models to a live dashboard. Built as a portfolio project demonstrating production-grade data engineering practices.

[![CI](https://github.com/charlesnet76/fintech-data-pipeline/actions/workflows/ci.yml/badge.svg)](https://github.com/charlesnet76/fintech-data-pipeline/actions)

## What it does

Ingests 5,000 mock Canadian FinTech transactions, validates and cleans the data with pandas, loads into PostgreSQL, transforms through dbt staging → fact → report layers, and serves analytics via a React dashboard.

## Architecture

```
CSV (raw data)
     │
     ▼
[ingestion/generate_data.py]   ← generates 5,000 mock transactions
     │
     ▼
[ingestion/ingest.py]          ← pandas ETL: extract, validate, load
     │
     ▼
[PostgreSQL]                   ← raw_transactions table
     │
     ▼
[transforms/dbt]               ← stg_transactions → fct_revenue → rpt_monthly
     │
     ▼
[dashboard]                    ← React + Recharts analytics UI
```

See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) for the full system design.

## Stack

| Layer         | Technology                          |
|---------------|-------------------------------------|
| Language      | Python 3.12                         |
| Data          | pandas, psycopg2                    |
| Database      | PostgreSQL 15                       |
| Transforms    | dbt (staging → fact → report)       |
| Orchestration | Docker Compose                      |
| CI/CD         | GitHub Actions                      |
| Dashboard     | React + Recharts                    |

## Quick start

**Requirements:** Docker Desktop, Python 3.10+

```bash
git clone https://github.com/charlesnet76/fintech-data-pipeline.git
cd fintech-data-pipeline

# Start PostgreSQL
docker compose up -d db

# Install Python dependencies
pip install pandas psycopg2-binary

# Generate mock data
python ingestion/generate_data.py

# Run ingestion
python ingestion/ingest.py
```

## Project structure

```
fintech-data-pipeline/
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions — validate + ingest tests
├── data/
│   └── transactions.csv        # Generated mock dataset (5,000 rows)
├── docs/
│   ├── PRD.md                  # Product requirements document
│   └── ARCHITECTURE.md         # System design + data flow
├── ingestion/
│   ├── generate_data.py        # Mock data generator
│   └── ingest.py               # pandas ETL — extract, validate, load
├── transforms/
│   └── dbt/                    # dbt project (coming in phase 2)
├── orchestration/
│   └── docker-compose.yml      # Full stack: DB + pipeline + dashboard
└── README.md
```

## CI/CD

Every push to `main` runs two automated jobs:

1. **validate-data** — generates dataset, runs 6 data quality checks
2. **test-ingestion** — spins up PostgreSQL, runs full ETL, verifies DB state

## Data quality checks

| Check | Rule |
|-------|------|
| Row count | Exactly 5,000 transactions |
| Nulls | Zero null values across all columns |
| Uniqueness | No duplicate `transaction_id` values |
| Amounts | All amounts > 0 |
| Statuses | Only `completed`, `pending`, `failed` |
| Revenue | Completed revenue > CAD $1,000,000 |

## Author

Carlos · Full Stack Developer · Database Engineer · Sidney, BC  
[github.com/charlesnet76](https://github.com/charlesnet76) · [charlesnet76.github.io](https://charlesnet76.github.io)

# AutoAnalyst 🔍

A modular, domain-aware automated data analysis tool that takes any financial dataset, runs the full analytics pipeline, and uses a **local LLM** to generate narrative insights — not just charts.

![Python](https://img.shields.io/badge/Python-3.11-blue?style=flat&logo=python)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?style=flat&logo=jupyter)
![Ollama](https://img.shields.io/badge/LLM-Llama%203%208B-green?style=flat)
![Cost](https://img.shields.io/badge/API%20Cost-Zero-brightgreen?style=flat)
![Status](https://img.shields.io/badge/Finance%20Module-Complete-success?style=flat)

---

## What Is This?

Most EDA tools give you charts and statistics. AutoAnalyst gives you **interpretation**.

The pipeline doesn't stop at "here's a correlation heatmap." It sends the computed summary to a locally running Llama 3 model and asks: *what does this actually mean? what's unusual? what should I investigate next?*

The result is a full analysis report — specific numbers, flagged anomalies, and recommended next steps — generated entirely on your machine with zero API cost.

---

## What Makes It Different

| Feature | Generic EDA Tools | AutoAnalyst |
|---|---|---|
| Auto-detects dataset type | ❌ | ✅ |
| Domain-specific analysis | ❌ | ✅ |
| LLM narrative insights | ❌ | ✅ |
| Runs fully local | ❌ | ✅ |
| Zero API cost | ❌ | ✅ |
| Chat with your data | ❌ | ✅ |
| Saves markdown reports | ❌ | ✅ |

---

## Tech Stack

| Component | Tool |
|---|---|
| Language | Python 3.11 |
| Notebooks | Jupyter (.ipynb) in VS Code |
| Data processing | pandas, numpy |
| Visualization | matplotlib, seaborn |
| LLM | Llama 3 8B via Ollama (local) |
| Reporting | Markdown |
| UI (planned) | Streamlit |

---

## Project Structure

```
AUTOANALYST/
│
├── README.md
├── project_plan.md
│
├── notebooks/
│   ├── 01_data_ingestion.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_eda_engine.ipynb
│   ├── 04_visualization.ipynb
│   └── 05_llm_insights.ipynb
│
└── finance_module/
    ├── datasets/
    │   ├── RELIANCE.NS.csv
    │   ├── Credit card transactions - India - Simple.csv
    │   ├── Annual_P_L_1_final.csv
    │   ├── Annual_P_L_2_final.csv
    │   ├── Quarter_P_L_1_final.csv
    │   ├── Quarter_P_L_2_final.csv
    │   ├── Balance_Sheet_final.csv
    │   ├── cash_flow_statments_final.csv
    │   ├── ratios_1_final.csv
    │   ├── ratios_2_final.csv
    │   ├── other_metrics_final.csv
    │   ├── price_final.csv
    │   └── t1_prices.csv
    │
    └── outputs/
        ├── charts/        ← auto-generated PNGs
        └── reports/       ← LLM insight reports (.md)
```

---

## How It Works

AutoAnalyst runs a 5-stage pipeline:

```
Dataset (CSV)
     ↓
01 — Ingestion       → detects dataset type, inspects structure
     ↓
02 — Cleaning        → type-aware cleaning, outlier flagging, type fixes
     ↓
03 — EDA Engine      → domain-specific statistical analysis
     ↓
04 — Visualization   → auto-generates 5 charts per dataset type
     ↓
05 — LLM Insights    → Llama 3 interprets results, generates report + chat
```

### Dataset Type Detection

The ingestion module automatically classifies any CSV into one of three finance types:

| Type | Identified By | Example |
|---|---|---|
| `timeseries` | OHLCV columns (open, high, low, close, volume) | Stock prices |
| `transactional` | amount, card, city, expense type columns | Credit card data |
| `fundamental` | sales, profit, EPS, BSE/NSE codes | Company financials |

Detection accuracy: **13/13 datasets correctly classified.**

---

## Notebooks

### `01_data_ingestion.ipynb`
- Loads any CSV with a smart loader that handles messy headers
- Reports shape, dtypes, missing values, duplicates
- Auto-detects finance dataset type with confidence scoring
- Outputs structured `ingestion_report` dictionary

### `02_data_cleaning.ipynb`
- Universal cleaning: deduplication, whitespace stripping, column standardization
- Type-specific cleaning:
  - **Timeseries** — date parsing, chronological sort, forward-fill missing prices, outlier flagging
  - **Transactional** — date parsing, city name normalization, category standardization, derived time features
  - **Fundamental** — sparse column dropping (>60% missing), numeric coercion, metric completeness flagging
- Outputs clean `df_clean` + `cleaning_report`

### `03_eda_engine.ipynb`
- Descriptive statistics and correlation matrix on all numeric columns
- Type-specific domain analysis:
  - **Timeseries** — daily returns, rolling MAs (7/30/90d), annualized volatility, yearly performance summary
  - **Transactional** — spend by category/card/gender/city/day-of-week, monthly trends
  - **Fundamental** — market cap distribution, profitability breakdown, sector analysis, key ratio summary
- Outputs structured `eda_summary` dictionary fed to the LLM

### `04_visualization.ipynb`
- Auto-selects and generates 5 charts based on detected dataset type
- **Timeseries:** price + MA overlay, volume bars (green/red by direction), return distribution, rolling volatility, correlation heatmap
- **Transactional:** category spend, monthly trend, card/gender pie charts, top cities, day-of-week pattern
- **Fundamental:** sector market cap, profitability distribution, EPS distribution, top companies, OPM vs market cap scatter
- All charts saved as PNG to `outputs/charts/`

### `05_llm_insights.ipynb`
- Builds a structured prompt from `eda_summary` — LLM never sees raw data
- Sends to Llama 3 8B via Ollama with temperature 0.3 for factual output
- Generates four sections: Executive Summary, Key Insights, Anomalies & Red Flags, Recommended Next Steps
- Saves full report as markdown to `outputs/reports/`
- Opens interactive chat loop for follow-up questions about the data

---

## Sample Output

### LLM Insight Report — RELIANCE.NS (Stock Data)

```
## EXECUTIVE SUMMARY
This dataset provides a comprehensive view of RELIANCE.NS stock performance
from August 2021 to August 2026. The stock is currently in a bearish phase,
trading below all major moving averages after reaching an all-time high of
₹1604.38 in January 2026.

## KEY INSIGHTS
1. Best year was 2025 with +29.78% annual return and a Sharpe Ratio of 2.06
2. Current price ₹1290.90 is below MA7 (₹1292), MA30 (₹1298), and MA90 (₹1327)
3. Worst single day: -7.49% on June 4, 2024
4. Annualized volatility averaged 21.65%, peaking at 37.03% in May 2022
5. Volume-return correlation of -0.029 suggests volume is not a reliable
   predictor of price direction for this stock
```

### Charts Generated (Finance Module)

| Dataset | Charts |
|---|---|
| RELIANCE.NS (Timeseries) | Price + MA, Volume, Returns, Volatility, Correlation |
| Credit Card (Transactional) | Category Spend, Monthly Trend, Card/Gender, Cities, Day Pattern |
| Annual P&L (Fundamental) | Sector Market Cap, Profitability, EPS, Top Companies, OPM Scatter |

---

## Setup & Usage

### Prerequisites

```bash
# Install Python dependencies
pip install pandas numpy matplotlib seaborn ollama

# Install Ollama
# Download from https://ollama.com and run:
ollama pull llama3
```

### Running the Pipeline

1. Clone the repo and place your CSV in `finance_module/datasets/`
2. Open `notebooks/` in VS Code
3. Run notebooks in order: `01` → `02` → `03` → `04` → `05`
4. Change `DATASET_FILENAME` in Cell 2 of each notebook to switch datasets
5. Insights report saved automatically to `finance_module/outputs/reports/`

### Switching Datasets

In Cell 2 of any notebook:

```python
# Change this one line to analyze any dataset
DATASET_FILENAME = 'RELIANCE.NS.csv'
```

---

## Roadmap

| Phase | Module | Status |
|---|---|---|
| 1 | Finance (Stock, Transactions, Fundamentals) | ✅ Complete |
| 2 | Sports (IPL, FIFA, NBA) | 🔄 In Progress |
| 3 | Healthcare | ⬜ Planned |
| 4 | E-Commerce | ⬜ Planned |
| 5 | Streamlit UI wrapper | ⬜ Planned |
| 6 | Deployment (Streamlit Cloud) | ⬜ Planned |

---

## Why Local LLM?

- **Zero cost** — no OpenAI, Gemini, or Anthropic API fees
- **No rate limits** — run as many analyses as you want
- **Privacy** — your financial data never leaves your machine
- **Offline capable** — works without internet after initial model download

---

## Author

**MD Rehan Ahmed**
BSc CSE (AI/ML) — Aspiring Data Analyst / Data Scientist

---

*Built from scratch. No AutoEDA wrappers. No shortcuts.*

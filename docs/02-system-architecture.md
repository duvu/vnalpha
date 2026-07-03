# 02. System architecture

## Architecture goal

`vnalpha` should be built as an end-of-day research platform with strong data lineage, deterministic signal logic, backtest validation, and AI-assisted explanation.

The system should not begin as an auto-trading platform.

## High-level architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                         UI Layer                             │
│   Streamlit / Next.js Dashboard / Watchlist / Journal        │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                       API Layer                              │
│             FastAPI / Query API / Report endpoints           │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                  Application Services                        │
│ Watchlist │ Pattern │ Backtest │ AI Report │ Journal │ Risk  │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                     Core Quant Engine                        │
│ Feature │ Pivot │ Pattern │ Scoring │ Outcome │ Regime       │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                  Research Data Warehouse                     │
│  Raw data │ Canonical OHLCV │ Features │ Patterns │ Outcomes │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                   Data Foundation Layer                      │
│      vnstock adapters, validation, provider diagnostics       │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                    External Data Providers                   │
│              KBS / VCI / DNSE / TCBS / others                │
└─────────────────────────────────────────────────────────────┘
```

## Major layers

### 1. Data foundation

Responsibility:

- fetch OHLCV and reference data;
- call `vnstock` as the primary data SDK;
- request validation reports where available;
- preserve provider metadata;
- avoid direct dependency on provider-specific endpoints in the research core.

Boundary:

`vnalpha` should not reimplement provider crawling or endpoint-specific logic if `vnstock` already provides it.

### 2. Research data warehouse

Responsibility:

- persist fetched data;
- store quality reports and provider diagnostics;
- build a canonical OHLCV dataset;
- version ingestion runs;
- provide reliable tables for feature engineering and backtests.

Recommended MVP storage:

```text
DuckDB + Parquet
```

Recommended later storage:

```text
PostgreSQL / TimescaleDB + object storage
```

### 3. Core quant engine

Responsibility:

- compute features;
- identify swing/pivot points;
- detect pattern candidates;
- score patterns;
- classify market regime;
- evaluate outcomes;
- prepare backtest datasets.

This is the main deterministic engine. It should be unit-tested and auditable.

### 4. Application services

Responsibility:

- produce daily watchlists;
- expose pattern details;
- run backtests;
- store journal entries;
- generate AI explanations and risk critiques.

### 5. UI layer

MVP UI:

```text
Streamlit
```

Later UI:

```text
FastAPI backend + Next.js frontend
```

## Service decomposition

MVP can be a modular monolith. Internally, use these modules:

```text
vnalpha/
├── ingestion
├── warehouse
├── features
├── pivots
├── patterns
├── scoring
├── regime
├── outcome
├── backtest
├── ai
├── dashboard
└── common
```

A future service split could be:

```text
data-service
feature-service
pattern-service
backtest-service
ai-report-service
api-service
dashboard
```

Do not split into microservices before the core scanner/backtest has proven useful.

## Daily processing flow

```text
1. sync_universe
2. sync_ohlcv
3. validate_data
4. build_canonical_ohlcv
5. compute_features
6. detect_pivots
7. detect_patterns
8. score_patterns
9. update_pattern_outcomes
10. generate_watchlist
11. generate_ai_explanations
12. publish_dashboard
```

## Key data contracts

### Canonical OHLCV

The feature engine must consume canonical data, not anonymous provider frames.

Minimum fields:

```text
symbol
time
interval
open
high
low
close
volume
selected_provider
quality_status
ingestion_run_id
```

### Pattern instance

Every detected pattern should be stored as a structured object.

Minimum fields:

```text
symbol
pattern_type
start_date
end_date
trigger_date
status
score
invalidation_price
feature_json
risk_json
data_quality_status
ingestion_run_id
```

### Pattern outcome

Every pattern should later receive forward outcome labels.

Minimum fields:

```text
pattern_id
horizon_days
forward_return
index_return
sector_return
excess_return
max_gain
max_drawdown
outcome_label
```

## Technology choices

### MVP

```text
Python
vnstock
Pandas / NumPy
DuckDB / Parquet
VectorBT
Streamlit
LiteLLM-compatible LLM gateway
```

### Later

```text
FastAPI
PostgreSQL / TimescaleDB
Prefect
MLflow
Next.js
OpenTelemetry / Grafana
```

## Architecture constraints

- AI does not create raw signals.
- The pattern engine must be deterministic.
- Every signal must be traceable to source data and feature values.
- Every pattern must be outcome-tracked.
- Backtests must avoid look-ahead bias.
- Production execution is out of scope for MVP.

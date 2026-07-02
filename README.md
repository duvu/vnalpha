# vnalpha

`vnalpha` is an AI-assisted market research and pattern-detection platform for Vietnamese equities.

The project focuses on end-of-day research workflows:

- ingest validated market data from `vnstock` or compatible adapters;
- build a canonical OHLCV research dataset;
- compute technical, liquidity, relative-strength, and market-regime features;
- detect structured price/volume patterns such as accumulation bases, breakouts, failed breakouts, VCP, and healthy pullbacks;
- track forward outcomes for every detected pattern;
- run backtests and parameter studies;
- generate AI-assisted explanations, risk critiques, daily reports, and trading journal notes.

## Core principle

`vnalpha` is a research and watchlist system, not an auto-trading bot.

The system should not place orders, connect to broker execution APIs, or let an LLM override deterministic risk rules. AI is used for explanation, critique, summarization, and research assistance. Signal generation must remain rule-based, testable, auditable, and validated by backtests/outcome tracking.

## Documentation

Start here:

- [Project overview](docs/README.md)
- [Vision and scope](docs/01-vision-and-scope.md)
- [System architecture](docs/02-system-architecture.md)
- [Data pipeline](docs/03-data-pipeline.md)
- [Pattern engine](docs/04-pattern-engine.md)
- [Backtest and outcome tracking](docs/05-backtest-and-outcome.md)
- [AI layer](docs/06-ai-layer.md)
- [Implementation roadmap](docs/07-implementation-roadmap.md)
- [Initial repository structure](docs/08-initial-repository-structure.md)

## Suggested MVP stack

- Python
- `vnstock` as the data foundation
- DuckDB + Parquet for local research storage
- Pandas / NumPy for feature engineering
- VectorBT for backtesting
- Streamlit for the first dashboard
- LiteLLM or a compatible LLM gateway for AI explanations

## Compliance note

This project is for personal research, education, and market analysis. It does not provide investment advice, does not guarantee performance, and should not be used for automated trading without a legally valid data source, broker API authorization, execution controls, and regulatory review.

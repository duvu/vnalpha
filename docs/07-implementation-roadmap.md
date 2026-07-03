# 07. Implementation roadmap

## Roadmap principle

Build the smallest useful research loop before adding AI complexity.

Correct order:

```text
data quality
→ feature store
→ pattern detection
→ outcome tracking
→ dashboard
→ AI explanation
→ backtest lab
→ ML ranking
```

Wrong order:

```text
multi-agent AI
→ prediction
→ auto-trading
→ later validation
```

## Phase 0. Repository foundation

Goal: create the project skeleton and configuration.

Deliverables:

```text
pyproject.toml
README.md
docs/
configs/
src/vnalpha/
tests/
```

Definition of done:

- project imports successfully;
- basic CLI entrypoint exists;
- lint/test command documented;
- docs define scope and architecture.

## Phase 1. Data ingestion and canonical store

Goal: ingest validated end-of-day OHLCV data and build a canonical dataset.

Deliverables:

```text
vnstock adapter
sync_universe
sync_ohlcv
quality_gate
build_canonical_ohlcv
DuckDB schema
Parquet export
```

Minimum tables:

```text
ingestion_run
market_ohlcv
canonical_ohlcv
ticker_master
```

Definition of done:

- can sync a small symbol universe;
- can sync at least 3 years of daily OHLCV;
- quality status is stored;
- canonical OHLCV is built;
- rejected symbols have exclusion reasons.

## Phase 2. Feature store

Goal: compute core technical and relative-strength features.

Deliverables:

```text
price features
volume features
volatility features
candle features
relative strength features
market breadth features
```

Minimum features:

```text
ma20
ma50
ma100
volume_ma20
volume_ratio
atr14
base_range_30d
close_strength
rs_20d
rs_60d
rs_line
rs_line_slope
distance_to_ma20
```

Definition of done:

- features are computed from canonical OHLCV only;
- feature table is reproducible by date;
- missing feature cases are handled explicitly;
- at least one benchmark index is supported.

## Phase 3. Pattern Engine v1

Goal: detect the first three useful patterns.

Patterns:

```text
ACCUMULATION_BASE
ACCUMULATION_BREAKOUT
FAILED_BREAKOUT
```

Deliverables:

```text
PatternDetector interface
AccumulationBaseDetector
AccumulationBreakoutDetector
FailedBreakoutDetector
Scoring engine
Risk flags
pattern_instance table
```

Definition of done:

- system scans the universe end-of-day;
- each pattern includes evidence features;
- each pattern has status and score;
- each pattern has invalidation logic;
- dashboard can list detected patterns.

## Phase 4. Outcome tracking

Goal: evaluate every detected pattern after fixed forward horizons.

Deliverables:

```text
pattern_outcome table
forward return calculator
excess return calculator
max gain/drawdown calculator
outcome labeler
```

Horizons:

```text
5 sessions
10 sessions
20 sessions
60 sessions
```

Definition of done:

- every pattern instance can be evaluated later;
- success/fail/neutral labels are assigned;
- outcome summary by pattern type is available;
- failed breakout rate is visible.

## Phase 5. Dashboard MVP

Goal: make the system usable daily.

MVP UI: Streamlit.

Screens:

```text
Market Overview
Pattern Watchlist
Pattern Detail
Failed Breakout Review
Outcome Summary
Journal
```

Definition of done:

- can filter by date, pattern type, score, sector, and status;
- can inspect pattern evidence;
- can view chart context;
- can see risk flags and invalidation level;
- can export watchlist.

## Phase 6. AI explanation and risk critique

Goal: add AI as an analyst layer, not a decision engine.

Deliverables:

```text
explain_pattern
risk_critic
daily_report
prompt templates
AI output cache
AI generation metadata
```

Definition of done:

- AI explanation is grounded in pattern JSON;
- AI risk critique separates setup, data, market, and execution risks;
- daily report summarizes only system-provided data;
- prompts are versioned;
- outputs are cached and stored.

## Phase 7. Backtest Lab

Goal: validate strategy rules beyond simple outcome tracking.

Deliverables:

```text
VectorBT integration
entry/exit simulation
fee/slippage assumptions
parameter testing
regime-level performance report
```

Definition of done:

- next-session entry assumption is supported;
- stop-loss and invalidation exits are supported;
- fees/slippage can be configured;
- results are split by year, sector, and market regime;
- strategy output can be compared with VN-Index/VN30 benchmark.

## Phase 8. Pattern Engine v2

Goal: add more sophisticated structures.

New detectors:

```text
VCP
HEALTHY_PULLBACK_TO_MA20
DISTRIBUTION_PATTERN
RELATIVE_STRENGTH_LEADER
```

Required addition:

```text
pivot engine
ATR-based pivot
ZigZag pivot
swing-level feature extraction
```

Definition of done:

- swing/pivot points are stored;
- VCP can identify contraction sequences;
- distribution patterns can warn about weakening setups.

## Phase 9. ML ranking

Goal: rank pattern candidates by estimated probability of successful outcome.

Do this only after enough pattern instances and outcomes exist.

Candidate models:

```text
LightGBM
XGBoost
RandomForest
Logistic regression baseline
```

Labels:

```text
success_20d
excess_return_positive_20d
risk_adjusted_success_20d
```

Definition of done:

- train/test split is time-based;
- model is compared with rule-based score;
- feature importance is reviewed;
- out-of-sample performance is reported;
- model cannot override risk gates.

## Phase 10. Paper trading simulation

Goal: test live workflow without real order execution.

Deliverables:

```text
paper portfolio
simulated entries/exits
journal integration
expected vs actual report
```

Definition of done:

- daily watchlist becomes simulated decisions;
- entries use next-session assumptions;
- PnL is tracked;
- paper results are compared with historical backtest expectations.

## Explicitly out of scope until later

```text
auto-trading
broker API execution
intraday scalping
reinforcement learning
LLM-only chart prediction
high-frequency strategy
commercial data resale
```

## Recommended first sprint

Build only:

```text
1. project skeleton
2. DuckDB schema
3. vnstock adapter
4. sync_ohlcv for a small universe
5. quality gate
6. canonical_ohlcv
7. basic MA/volume/RS features
8. simple accumulation base detector
```

This is enough to validate whether the foundation works before expanding.

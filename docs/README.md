# vnalpha documentation

`vnalpha` is designed as an AI-assisted Pattern Detection and Watchlist Platform for Vietnamese equities.

The documentation is organized around the main engineering concerns:

1. **Vision and scope** — what the system should and should not do.
2. **System architecture** — major services, data stores, and execution flow.
3. **Data pipeline** — how `vnstock` data is ingested, validated, canonicalized, and stored.
4. **Pattern engine** — how price/volume patterns are represented, detected, scored, and invalidated.
5. **Backtest and outcome tracking** — how every detected pattern is evaluated after 5/10/20/60 sessions.
6. **AI layer** — how LLMs are used safely for explanation, critique, reporting, and research assistance.
7. **Roadmap** — an implementation sequence that avoids overbuilding before the core signal logic is validated.
8. **Repository structure** — a practical starting layout for the codebase.

## Design stance

The project should start as an **end-of-day research system**:

```text
validated market data
→ canonical OHLCV
→ feature store
→ pivot engine
→ pattern engine
→ outcome tracker
→ backtest
→ AI explanation
→ dashboard and journal
```

It should not start as:

```text
LLM sees chart
→ LLM predicts price
→ bot places order
```

The second approach is hard to audit, easy to overfit, and unsafe for real-money trading.

## First target outcome

The first useful version of `vnalpha` should produce a daily watchlist with clear evidence:

- ticker;
- pattern type;
- pattern score;
- setup status;
- base range;
- volume confirmation;
- relative strength;
- market regime;
- risk flags;
- invalidation level;
- AI-generated explanation;
- later forward outcome.

Example:

```text
Ticker: FPT
Pattern: ACCUMULATION_BREAKOUT
Score: 86
Status: CONFIRMED
Evidence:
- 38-session base, 8.7% range
- breakout volume 1.9x 20-session average
- close strength 0.84
- RS 20d +6.4% versus VN-Index
Risk:
- price 7.8% above MA20
- sector confirmation weak
Invalidation:
- close back below breakout level or MA20
```

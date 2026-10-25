# Trading skills (vendored)

Project-level copies of selected trading skills. Each skill folder is unmodified upstream content.

| Source | License | Skills |
|---|---|---|
| [tradermonty/claude-trading-skills](https://github.com/tradermonty/claude-trading-skills) | MIT (`LICENSE-tradermonty`) | trade-performance-coach, weekly-performance-digest, trader-memory-core, signal-postmortem, position-sizer, futures-position-sizer, drawdown-circuit-breaker, pre-trade-discipline-gate, exposure-coach, backtest-expert, edge-strategy-reviewer, trade-hypothesis-ideator, technical-analyst, mt5-robot-tester, strategy-pivot-designer, macro-regime-detector, market-environment-analysis |
| [agiprolabs/claude-trading-skills](https://github.com/agiprolabs/claude-trading-skills) | MIT (`LICENSE-agiprolabs.md`) | trade-journal, position-sizing, kelly-criterion, risk-management, portfolio-analytics, exit-strategies, walk-forward-validation, vectorbt, backtrader, strategy-framework, pandas-ta, ta-lib, volatility-modeling, regime-detection, mean-reversion, correlation-analysis, trading-visualization, ohlcv-processing |

OpenMobius-skill (ICT/SMC) is not vendored here because it needs a ~800 MB local install; install it with
`git clone https://github.com/MobiusQuant/OpenMobius-skill && cd OpenMobius-skill && python3 install.py --platform claude-code`.

Optional keys: `FMP_API_KEY` (Financial Modeling Prep, free tier) for some tradermonty skills.

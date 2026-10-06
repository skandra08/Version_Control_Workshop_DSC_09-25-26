# quantlab

A small, bias-aware backtesting and strategy-validation framework in Python. The focus is
not on finding a magic strategy but on **not fooling yourself**: look-ahead protection,
transaction costs, walk-forward validation, and multiple-testing-adjusted Sharpe ratios.

## Features
- **Vectorised backtest engine** with enforced one-bar signal lag and per-turnover costs
- **Signals**: cross-sectional momentum and mean reversion (dollar-neutral)
- **Walk-forward validation**: parameters chosen in-sample, traded strictly out-of-sample
- **Metrics**: Sharpe, drawdown, Calmar, turnover, Probabilistic Sharpe and
  Deflated Sharpe (Bailey & López de Prado) to penalise parameter mining
- **Reproducible synthetic data** with a tunable autocorrelation edge, so tests can assert
  that the framework detects real signal and rejects noise
- Pytest suite + GitHub Actions CI

## Quick start
```bash
pip install -e ".[dev]"
pytest -q
python examples/run_demo.py
```

## Demo result (walk-forward momentum, 12 assets, 3000 days, 5 bps costs)
| Data | OOS Sharpe | Max DD | PSR | Deflated Sharpe |
|---|---|---|---|---|
| Trending (AR phi=0.1) | 0.49 | -21.6% | 0.94 | 0.29 |
| Pure noise | -0.30 | -35.0% | 0.17 | 0.08 |

The edge looks significant under PSR but not after deflating for the 8 parameter
combinations tried, which is the point of the exercise.

## Design notes
- A weight decided at close `t` earns the return `t -> t+1`. `tests/` includes a regression
  test showing that a naive same-bar pairing yields an absurd Sharpe, while the engine does not.
- Signals only use past data, so computing them on the full history is leak-free; the
  walk-forward layer is where selection leakage is controlled.

## Roadmap
- [ ] Real data loader (yfinance/CSV) and S&P universe survivorship-bias discussion
- [ ] Combinatorial purged cross-validation
- [ ] Volatility targeting and mean-variance portfolio construction
- [ ] Limit order book / market-making simulator module

# Codebase Overview

## Repository shape
- The repository currently contains a single Jupyter notebook: `Regime3_Bestandteile.ipynb`.
- The notebook is a research/backtesting workflow for a polynomial-regression trend-following strategy on PLTR price data.

## End-to-end workflow in the notebook
1. **Data source setup**
   - Stores an EODHD API key in a variable (`eod`) and demonstrates pulling end-of-day OHLCV data from EODHD.
   - Provides an alternate path to download the same market data from `yfinance`.
2. **Charting and exploratory visualization**
   - Renders candlestick charts with `mplfinance`.
   - Defines a helper to overlay Bollinger Bands on the candlestick chart.
3. **Polynomial trend model**
   - Fits an N-degree polynomial regression (`PolynomialFeatures` + `LinearRegression`) to close prices over time.
   - Plots actual close prices against polynomial-fit trend values.
4. **Model diagnostics**
   - Computes fit quality metrics including R², MAE, RMSE, and MAPE.
5. **Signal generation (bias-reduced expanding window)**
   - Recomputes `poly_fit` using an expanding-window loop to avoid lookahead bias.
   - Derives trend slope (`reg_slope`) and creates long-only signals when:
     - close > poly_fit, and
     - regression slope is positive.
   - Creates `entry` and `exit` booleans from position transitions.
6. **Backtest engine**
   - Builds daily strategy returns from position state.
   - Applies per-trade transaction costs (0.1%).
   - Produces cumulative equity curves for:
     - buy-and-hold,
     - strategy gross,
     - strategy net of transaction costs.
7. **Performance analytics**
   - Calculates total return, CAGR, annualized volatility, Sharpe ratio, and max drawdown.
   - Computes trade-level stats (count, win rate, average trade return).
8. **Out-of-sample evaluation**
   - Splits dataset 90/10 into in-sample/out-of-sample.
   - Repeats polynomial-fit signal logic on the OOS slice.
   - Compares OOS cumulative performance and prints OOS metrics.

## Key libraries
- `pandas`, `numpy` for data processing.
- `matplotlib`, `mplfinance` for plotting.
- `scikit-learn` (`PolynomialFeatures`, `LinearRegression`, metrics) for modeling and evaluation.
- `yfinance`, `eodhd` for market data ingestion.

## Practical notes
- The notebook writes intermediate outputs to Excel (`output.xlsx`, `output_trans.xlsx`).
- There is no package/module layout yet (no `src/`, tests, or reusable Python package); this is currently a notebook-first research repo.
- The EOD key is embedded in notebook variables, so rotating/securing credentials would be advisable before sharing publicly.

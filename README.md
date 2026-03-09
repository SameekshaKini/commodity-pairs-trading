# 📈 Commodity Futures Pairs Trading

**Time Series Analysis — Final Project**  
Statistical arbitrage on commodity futures using cointegration, Ornstein-Uhlenbeck mean reversion, and walk-forward validation.

---

## Overview

This project implements a full **market-neutral pairs trading strategy** on 22 commodity futures (2000–2023). Rather than betting on price direction, the strategy trades the *spread* between two cointegrated commodities back toward its long-run equilibrium whenever it deviates unusually far.

**Key design features:**
- Dual stationarity testing (ADF + KPSS) before any cointegration work
- Engle-Granger screening across all 190 possible pairs
- Economic + unit-compatibility filter to reject spurious pairs
- Rolling windows **calibrated to each pair's own half-life** (2×HL rule) instead of a fixed window
- Three models: Static OLS, Rolling OLS, VECM — compared on in-sample, walk-forward CV, and OOS
- Stateful signal generator with stop loss — no look-ahead, no double-entry bug
- Walk-forward cross-validation (5 folds, expanding window)
- Strict out-of-sample holdout: 2020–2023 (COVID crash, WTI negative price, Russia-Ukraine)
- Granger causality to identify price leadership within pairs
- Ornstein-Uhlenbeck forecasting with CI coverage as a regime-change alarm

---

## The Four Pairs

| Pair | Sector | Half-life | Rolling Window | OOS Sharpe |
|------|--------|-----------|---------------|------------|
| WTI Crude / Brent Crude | Energy | 21.9 days | 44 days | −0.44 |
| Low Sulphur Gas Oil / ULS Diesel | Energy | 5.8 days | 20 days | −0.75 |
| Soybeans / Soybean Meal | Agriculture | 85.6 days | 171 days | **+0.81** |
| Corn / Wheat | Agriculture | 84.4 days | 169 days | −0.13 |

Agricultural pairs outperformed energy pairs OOS because crush economics and feed-grain substitution are enforced by physical supply chains that are resilient to geopolitical disruptions. Energy pairs were hurt by the 2022 Russia-Ukraine energy restructuring.

---

## Repository Structure

```
commodity-pairs-trading/
│
├── data/
│   └── commodity_futures.csv        # 22 commodity futures, 2000–2023
│
├── notebooks/
│   ├── Pairs_Trading_Final_v6.ipynb # Main notebook — run this
│   └── Granger_OU_Addon.ipynb       # Standalone Granger + OU deep-dive
│
├── docs/
│   ├── Project_Report_v6.docx       # Full project report (8 grading criteria)
│   └── Pairs_Trading_Explanation_v6.docx  # End-to-end concept + code guide
│
├── results/
│   └── (auto-generated plots and tables when you run the notebook)
│
├── requirements.txt
├── .gitignore
└── README.md
```

---

## Quickstart

### 1. Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/commodity-pairs-trading.git
cd commodity-pairs-trading
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Run the notebook
```bash
jupyter notebook notebooks/Pairs_Trading_Final_v6.ipynb
```

The notebook reads `data/commodity_futures.csv` directly. No other setup needed — all 60 cells run end to end in under 3 minutes.

---

## Notebook Structure (60 cells)

| Section | What It Does |
|---------|-------------|
| 0 — Imports | Libraries, plot config |
| 1 — EDA | Price histories, sector indices, correlation heatmap |
| 2 — Stationarity | ADF + KPSS on all 22 commodities |
| 3 — Cointegration Screening | Engle-Granger on all 190 pairs |
| 4 — Pair Selection | Economic + unit-compatibility filter → 4 pairs |
| 5 — Feature Engineering | β, spread, half-life, **2×HL rolling windows** |
| 6 — Signal Generation & Backtesting | Static OLS, Rolling OLS, VECM |
| 7 — Spread Diagnostics | ADF, ACF, histogram, QQ plot per pair |
| 8 — Walk-Forward CV | 5-fold expanding window, no look-ahead bias |
| 9 — OOS Backtest | 2020–2023 stress test, frozen parameters |
| 10 — Model Comparison | IS, OOS, CV ranked for all 12 model-pair combinations |
| 11 — Granger Causality | Price leadership at lags 1, 2, 5, 10 |
| 12 — OU Train/Test | Fit on 2000–Feb 2023, validate on last 6 months |
| 13 — OU Forward Forecast | Full-history OU projection to 2025 |
| 14 — Summary | Full design decisions and results table |

---

## Key Results

### In-Sample (2000–2023, Static OLS)
| Pair | IS Sharpe | Trades | Win Rate |
|------|-----------|--------|----------|
| Gas Oil / Diesel | 1.312 | 295 | 99.0% |
| Corn / Wheat | 0.382 | 63 | 73.0% |
| Soybeans / Meal | 0.586 | 63 | 77.8% |
| WTI / Brent | 0.301 | 170 | 82.9% |

### Out-of-Sample (2020–2023, parameters frozen at end-2019)
| Pair | OOS Sharpe | Max Drawdown | Trades | Key Finding |
|------|------------|-------------|--------|-------------|
| Gas Oil / Diesel | −0.753 | −30.1% | 48 | Russia-Ukraine regime break |
| WTI / Brent | −0.440 | −11.4% | 2 | Sanctions compressed spread |
| Soybeans / Meal | **+0.806** | −1.7% | 2 | Crush economics regime-stable |
| Corn / Wheat | −0.133 | −4.3% | 3 | Ukraine grain shock delayed reversion |

### Walk-Forward CV (5 folds)
| Pair | Active Folds | CV Mean Sharpe |
|------|-------------|---------------|
| Gas Oil / Diesel | 5/5 | +1.424 |
| WTI / Brent | 4/5 | +0.855 |
| Soybeans / Meal | 4/5 | +0.708 |
| Corn / Wheat | 4/5 | −0.212 |

---

## The v6 Innovation: Half-Life-Based Rolling Windows

**Rule:** `window = max(round(2 × half_life), 20)`

| Pair | Old Window | New Window | IS Sharpe |
|------|-----------|-----------|-----------------|
| WTI / Brent | 60d | 44d |0.301 |
| Gas Oil / Diesel | 60d | 20d | 1.312 |
| Soybeans / Meal | 60d | 171d | **0.586** |
| Corn / Wheat | 60d | 169d | 0.382 |

---

## Core Concepts Used

| Concept | Where Applied |
|---------|--------------|
| I(1) Non-Stationarity | Section 2 — prerequisite for cointegration |
| Engle-Granger Cointegration | Section 3 — 190-pair screen |
| OLS Hedge Ratio | Section 5 — β = Cov(Y,X)/Var(X) |
| Ornstein-Uhlenbeck Process | Sections 12–13 — mean reversion model |
| AR(1) → Half-life | Section 5 — HL = ln(2)/κ |
| Walk-Forward CV | Section 8 — time-safe validation |
| Granger Causality | Section 11 — price leadership |
| VECM | Section 6.3 — joint ML estimation |
| Z-Score Signals | Sections 5–6 — ±2σ entry, ±0.5σ exit, ±4σ stop |

---

## Dependencies

See `requirements.txt`. Main packages:
- `pandas`, `numpy`, `matplotlib`, `seaborn`
- `statsmodels` — ADF, KPSS, cointegration, VECM, rolling OLS, Granger
- `scipy` — stats utilities
- `yfinance` — optional, for post-2023 actual price verification (Section 13.1)


---

## Citation / Course
  
Dataset: Commodity Futures Daily Prices (2000–2023)

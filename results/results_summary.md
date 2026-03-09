# Results Summary

All numbers from the executed v6 notebook. Parameters trained on 2000–2019, tested on 2020–2023.

## Pair Metadata

| Pair | β (hedge ratio) | Half-life | Rolling Window | Cointegration p |
|------|----------------|-----------|---------------|----------------|
| WTI Crude / Brent Crude | 1.1093 | 21.9 days | 44 days | 0.0015 |
| Low Sulphur Gas Oil / ULS Diesel | 0.3198 | 5.8 days | 20 days | 0.0000 |
| Soybeans / Soybean Meal | 0.2708 | 85.6 days | 171 days | 0.0156 |
| Corn / Wheat | 1.0174 | 84.4 days | 169 days | 0.0008 |

## In-Sample Performance (2000–2023, Static OLS)

| Pair | IS Sharpe | Max Drawdown | Win Rate | Trades |
|------|-----------|-------------|----------|--------|
| Gas Oil / Diesel | 1.312 | −16.6% | 99.0% | 295 |
| Soybeans / Meal | 0.586 | −2.8% | 77.8% | 63 |
| Corn / Wheat | 0.382 | −4.4% | 73.0% | 63 |
| WTI / Brent | 0.301 | −12.4% | 82.9% | 170 |

## Walk-Forward Cross-Validation (5 folds, 2018–2023)

| Pair | Window | Active Folds | Mean Sharpe | Std |
|------|--------|-------------|------------|-----|
| Gas Oil / Diesel | 20d | 5/5 | +1.424 | ±1.550 |
| WTI / Brent | 44d | 4/5 | +0.855 | ±1.105 |
| Soybeans / Meal | 171d | 4/5 | +0.708 | ±0.833 |
| Corn / Wheat | 169d | 4/5 | −0.212 | ±0.947 |

## Out-of-Sample Backtest (2020–2023, ±2σ entry)

| Pair | OOS Sharpe | Max Drawdown | Win Rate | Trades | Main Factor |
|------|------------|-------------|----------|--------|-------------|
| Soybeans / Meal | +0.806 | −1.7% | 100.0% | 2 | Crush economics held through all regimes |
| Corn / Wheat | −0.133 | −4.3% | 33.3% | 3 | Ukraine grain shock delayed reversion 4 months |
| WTI / Brent | −0.440 | −11.4% | 50.0% | 2 | Sanctions compressed spread; only 2 trades |
| Gas Oil / Diesel | −0.753 | −30.1% | 16.7% | 48 | Russia-Ukraine structural regime break |

## OU Model Validation (Train: 2000–Feb 2023, Test: Feb–Aug 2023)

| Pair | κ | θ | σ | HL | 50% CI | 90% CI | RMSE |
|------|---|---|---|----|--------|--------|------|
| WTI / Brent | 0.0320 | −3.45 | 1.326 | 21.7d | 99.2% | 100.0% | 0.977 |
| Gas Oil / Diesel | 0.1312 | 4.59 | 4.494 | 5.3d | 2.3% | 74.6% | 13.60 |
| Soybeans / Meal | 0.0078 | 38.41 | 3.919 | 89.4d | 40.0% | 89.2% | 26.03 |
| Corn / Wheat | 0.0082 | 130.53 | 11.435 | 84.6d | 39.2% | 96.9% | 67.42 |

Gas Oil/Diesel's 2.3% coverage confirms a structural regime break — the OU model correctly identified the long-run equilibrium but the Russia-Ukraine invasion permanently elevated the European gas premium above that level for the entire test window.

## Granger Causality Summary

| Pair | Result | Leader |
|------|--------|--------|
| WTI / Brent | Bidirectional from Lag 1 | Neither — simultaneous |
| Gas Oil / ULS Diesel | ULS Diesel leads Gas Oil from Lag 1 | US market (higher CME liquidity) |
| Soybeans / Soybean Meal | Bidirectional from Lag 1 | Neither — crush processors respond to both |
| Corn / Wheat | Corn leads Wheat (Lags 1–10 dominant) | CBOT Corn (CME) price-discovers first |

## v5 → v6 Rolling Window Impact

| Pair | Window | IS Sharpe | CV Mean |
|------|----------|----------|-------------|
| WTI / Brent | 44d | 0.301 | +0.855 (4/5) |
| Gas Oil / Diesel | 20d | 1.312 | +1.424 (5/5) |
| Soybeans / Meal | 171d | 0.586 | +0.708 (4/5) |
| Corn / Wheat | 169d | 0.382 | −0.212 (4/5) |

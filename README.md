[README (1).md](https://github.com/user-attachments/files/26985525/README.1.md)
# GARCH Volatility Forecasting — Benchmark Study

> Tested 6 GARCH variants against simple persistence across 2,340 US equities. Spoiler: yesterday's volatility beats every model.

---

## Overview

This project tests whether GARCH-family models add predictive value over naive persistence when forecasting **daily realized volatility** across a large cross-section of US equities.

The answer is no — at daily frequency, volatility is sticky enough that Lag(1) realized vol outperforms every fitted model by a factor of 2–5×. That's the result, and it's a useful one.

**Data:** 2,340 US equities · 2000–2025 · ~13M daily price records  
**Models tested:** ARCH, GARCH(1,1), GARCH(2,1), EGARCH, TGARCH (GJR), GARCH-M

---

## Methodology

**Realized volatility** is defined as the 20-day rolling standard deviation of daily returns — the standard industry measure for what actually happened.

Each stock is evaluated on its most recent data:

| Component | Value | Rationale |
|---|---|---|
| Training window | 252 days | 1 trading year; stable parameter estimates |
| Test window | 63 days | ~3 months; ~44 rolling evaluation windows |
| Realized vol window | 20 days | ~1 month; balances responsiveness and noise |
| Prediction horizon | 1-step ahead | Where GARCH clustering effects are strongest |

**Benchmarks:**
- **Lag(1)** — yesterday's realized vol predicts today's
- **Lag(2)** — two days ago's realized vol predicts today's

**Key metric:** RMSE ratio = GARCH error / Lag error. Values below 1.0 mean GARCH wins.

---

## Results

GARCH loses across every sector and every model variant.

| Sector | Best Model | RMSE Ratio vs Lag(1) |
|---|---|---|
| CONSUMER | TGARCH | 1.33 |
| FINANCE | EGARCH | 1.72 |
| INDUSTRIALS | GARCH(2,1) | 2.26 |
| AUTO | EGARCH | 2.69 |
| UTILITIES | TGARCH | 2.95 |
| TECH | TGARCH | 2.87 |
| MATERIALS | TGARCH | 3.08 |
| HEALTHCARE | EGARCH | 3.25 |

All ratios above 1.0. Lag(1) wins everywhere.

**Why:**
1. Daily data — volatility clustering is most pronounced intraday, not day-to-day
2. Static forecast — a single GARCH estimate held for 63 days can't adapt; Lag(1) updates daily
3. Calm regime — persistent, low-amplitude vol in recent data makes naive persistence hard to beat

---

## Repo Structure

```
├── GARCH_Volatility_Study.ipynb   # Full analysis notebook
├── cluster_by_sector.py           # Sector assignment pipeline
├── filter_stock_prices.py         # Data cleaning
├── README.md
└── .gitignore
```

---

## Usage

Clone the repo and run the notebook top-to-bottom. You'll need your own price data in the format:

```
date, ticker, close
2000-01-03, AAPL, 25.94
...
```

And a sector mapping file:

```
ticker, sector
AAPL, TECH
...
```

Install dependencies:

```bash
pip install arch pandas numpy matplotlib seaborn tqdm
```

---

## Stack

`Python 3.10` · `arch` · `pandas` · `numpy` · `matplotlib` · `seaborn`

---

## Notes

Price data and sector assignments are not included in this repo (file size + licensing). The notebook expects `prices_fixed.csv` and `ticker_sectors_filled.csv` in the working directory.

# Kalman Filter Forecasting Strategy for S&P 500 Tech Sector

A state-space model with time-varying coefficients that fuses Bloomberg fundamental data with Google Trends sentiment indicators to forecast weekly returns on the S&P 500 Information Technology Sector Index (S5INFT). The strategy achieves a Sharpe ratio of 1.03 versus 0.95 for buy-and-hold over a 7-year out-of-sample backtest (2019 to 2026), generating 10.7% annualized alpha.

## Key Results

| Metric | Strategy | Buy-and-Hold |
|--------|----------|--------------|
| Sharpe Ratio | 1.03 | 0.95 |
| Annualized Alpha | 10.7% | N/A |
| Out-of-Sample Period | 2019 to 2026 | 2019 to 2026 |
| Weighted Directional Test | p = 0.01 | N/A |
| Binomial Test | Not significant | N/A |

The divergence between the Binomial and Weighted Directional tests is itself an interesting result: the strategy does not predict direction significantly more than 50% of the time, but the weeks where it correctly moves to cash are disproportionately high-loss weeks. In other words, the model's value lies in avoiding large drawdowns rather than in consistently calling direction.

## Methodology

The model uses a Kalman filter with three time-varying state variables, each capturing a different information channel:

| Predictor | Source | Frequency | Economic Rationale |
|-----------|--------|-----------|-------------------|
| Forward EPS Momentum | Bloomberg (S5INFT BestEPS) | Weekly | Captures shifts in analyst consensus on tech sector earnings |
| "Tech Layoffs" Search Volume | Google Trends | Monthly (interpolated) | Proxies labor market fear and sector-specific pessimism |
| "Artificial Intelligence" Search Volume | Google Trends | Monthly (interpolated) | Proxies hype and speculative demand in the tech sector |

The Kalman filter allows each predictor's coefficient to evolve over time, accommodating the reality that the relationship between sentiment indicators and returns is not stable across market regimes. An expanding-window walk-forward optimization protocol recalibrates the filter annually with no look-ahead bias.

The dual-sentiment architecture is a deliberate design choice: "tech layoffs" captures the fear tail of sentiment while "artificial intelligence" captures the hype tail, giving the model exposure to both extremes of the distribution.

## Strategy Design

The strategy generates a one-step-ahead return forecast each week. When the forecast exceeds a threshold (τ = −0.005, calibrated via walk-forward optimization), the portfolio is fully invested in S5INFT. When the forecast falls below the threshold, the portfolio moves to the risk-free asset (1-Year Treasury).

The negative threshold reflects the model's asymmetric objective: it is more valuable to avoid large drawdowns than to capture small positive weeks. The strategy is long approximately 91% of the time, making it functionally close to buy-and-hold, but the 9% of weeks spent in cash are well-timed to avoid significant losses.

## Data

**Included in this repository (publicly available):**
- `DGS1.xlsx`, 1-Year Treasury yield from FRED
- `Tech Layoffs.xlsx`, Google Trends monthly search volume (US)
- `Artificial Intelligence.xlsx`, Google Trends monthly search volume (US)

**Must be obtained separately (Bloomberg Terminal, proprietary):**
- `S5INFT- Last Price (Weekly).xlsx`, S5INFT index weekly closing price
- `S5INFT- BestEPS (Weekly).xlsx`, 12-month forward Best EPS consensus estimate
- `SPX- Last Price (Weekly).xlsx`, S&P 500 index weekly closing price

To reproduce: export these three series as weekly frequency .xlsx files from the Bloomberg Terminal covering January 2010 to March 2026. Place them in the same directory as the notebook.

## Repository Structure

```
├── Forecasting_the_S_P_500_Information_Technology_Sector__S5INFT__Using_a_Kalman_Filter.ipynb
├── README.md
├── DGS1.xlsx                          # 1-Year Treasury yield (FRED)
├── Tech Layoffs.xlsx                  # Google Trends monthly data
└── Artificial Intelligence.xlsx       # Google Trends monthly data
```

## How to Run

1. Obtain the three Bloomberg .xlsx files as described above and place them in the project directory alongside the included data files.
2. Install dependencies: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `statsmodels`, `openpyxl`.
3. Open the notebook in Jupyter and run all cells sequentially.
4. All visualizations and the final performance metrics table are generated inline.

## Environment

Python 3.10, with `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `statsmodels`, `openpyxl`.

## Context

This project was developed as the final project for Econ 409 (Forecasting Asset Prices) at UCLA, Winter 2026.

## Author

**Rudransh Khera**, Master of Quantitative Economics, UCLA
- [GitHub](https://github.com/kherarudransh-oss)
- [LinkedIn](https://www.linkedin.com/in/rudransh-khera/)

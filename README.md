# Portfolio optimization and covariance risk

[中文](README.zh-CN.md)

Three notebooks on CSI 300 constituents. Index weights and daily returns are already cached in `data/`, so the notebooks run without a data-vendor token.

## Notebooks

- `drill1_erc_verify.ipynb`  
  Equal-risk-contribution weights for the 50 largest names. Risk contributions sit near 1/50. One holding window, October 2026 (17 trading days), compares rescaled index weights with those weights. The ratio in that table is realized return over a model volatility from the estimation sample. It is not a Sharpe ratio, and it is not a track record.

- `drill2_ledoitwolf_compare.ipynb`  
  Sample covariance against `shrink_cov`, a simplified Ledoit-Wolf shrinkage toward a scaled identity. Each day from 2024-01-02 to 2026-05-29 uses the previous month's index weights, the 100 names with a complete prior 60 days, and a long-only global minimum-variance portfolio. Shrinkage pulls the median condition number from about 1e19 down to about 100. Mean forward 60-day realized volatility stays near 11.8% and 12.0%. This minimum-variance problem does not invert the covariance, so the conditioning gain does not show up as lower realized volatility.

- `drill4_cvxpy_optimizer.ipynb`  
  Long-only minimum variance with cvxpy. The demos set a 5% cap on each name. `cp_weight` defaults that cap to 1. On this estimation window the capped minimum-variance portfolio already clears a 10% annualized mean target inside the sample, so adding that target does not move the weights. The turnover penalty is left at zero. The solver is CLARABEL.

## Layout

`edhec_risk_kit.py` holds the estimators and the optimizer used above: `shrink_cov`, `equal_risk_contributions`, `risk_contribution`, `cp_weight`, and `backtest_hs300`. Cached weights, returns, the Drill 2 daily results, and the comparison figure are in `data/`.

## Run

Create the environment from the pinned versions in `environment.yml`, then start Jupyter from this directory:

```bash
conda env create -f environment.yml
conda activate quant
jupyter notebook
```

The notebooks read the cached frames in `data/`. `get_hs300_weight` and `get_returns` are the only functions that download data. They read `TUSHARE_TOKEN` from the environment, so a terminal opened after the variable is set will see it. The notebooks do not call those functions.

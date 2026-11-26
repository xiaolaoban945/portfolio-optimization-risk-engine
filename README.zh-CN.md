# 组合优化与协方差风险

[English](README.md)

三个笔记本使用沪深 300 成分股。指数权重和日收益已经缓存在 `data/`，所以笔记本运行时不需要数据商 token。

## 笔记本

- `drill1_erc_verify.ipynb`  
  给指数权重最大的 50 只股票做风险平价。风险贡献靠近 1/50。对照窗口只有 2026 年 10 月的 17 个交易日，比较的是重新归一的指数权重和这组风险平价权重。表里的比值是实现收益除以估计样本上的模型波动。它不是夏普比率，也不是业绩记录。

- `drill2_ledoitwolf_compare.ipynb`  
  比较样本协方差和 `shrink_cov`。后者是向平均方差乘单位阵收缩的简化 Ledoit–Wolf。2024-01-02 到 2026-05-29 的每个交易日，用上一个月的指数权重，取过去 60 天数据完整、权重最大的 100 只，做只做多的全局最小方差。收缩把条件数中位数从大约 1e19 降到大约 100。之后 60 天的已实现波动均值大约是 11.8% 和 12.0%。这个最小方差问题不对协方差求逆，所以条件数的改善没有变成更低的样本外波动。

- `drill4_cvxpy_optimizer.ipynb`  
  用 cvxpy 做只做多、权重和为 1 的最小方差。演示时把单票上限设为 5%。`cp_weight` 的默认上限是 1。在这段估计样本里，带 5% 上限的最小方差组合已经超过 10% 年化的样本内均值目标，所以加上收益目标没有改变权重。换手惩罚保持为 0。求解器是 CLARABEL。

## 文件

`edhec_risk_kit.py` 放着上面用到的估计和优化函数：`shrink_cov`、`equal_risk_contributions`、`risk_contribution`、`cp_weight`、`backtest_hs300`。缓存的权重、收益、Drill 2 的每日结果和对比图在 `data/`。

## 运行

用 `environment.yml` 里钉住的版本创建环境，然后在这个目录启动 Jupyter：

```bash
conda env create -f environment.yml
conda activate quant
jupyter notebook
```

笔记本读取 `data/` 里的缓存。只有 `get_hs300_weight` 和 `get_returns` 会下载数据，它们从环境变量读取 `TUSHARE_TOKEN`。变量配好之后，新开的终端才能看到。笔记本不会调用这两个函数。

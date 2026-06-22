---
title: OOS Backtest Harness
type: concept
date: 2026-06-22
tags: [backtest, oos, dsr, fees, slippage]
---

# OOS Backtest Harness

`src/lib/__tests__/oos-backtest.test.ts` — the baseline measurement system for every quant improvement iteration.

## Setup
- 365 synthetic daily klines (seeded RNG, deterministic)
- 70/30 train/test split (255 train, 110 test)
- Realistic costs: feePct=0.05%, slippagePct=0.02% per side (~0.14% round-trip)
- 3 preset strategies: mean_reversion, trend_following, momentum_breakout

## Baseline OOS Metrics (with costs)

| Strategy | Sharpe | maxDD | WinRate | Trades | DSR |
|----------|--------|-------|---------|--------|-----|
| mean_reversion | -0.093 | 0.9% | 50% | 2 | 0.058 |
| trend_following | -2.432 | 1.0% | 0% | 4 | 0.000 |
| momentum_breakout | 0.349 | 0.5% | 50% | 2 | 0.126 |

## DSR Gate
All 3 presets have DSR < 0.95 → all rejected. None have real out-of-sample edge after realistic costs.

## Fee Impact
momentum_breakout train Sharpe dropped from 0.624 (no costs) to 0.142 (with costs) — 77% reduction. The strategy was overfit to a costless world.

See also: [[fee-slippage-model]], [[dsr-gate]], [[analysis-deflated-sharpe]]

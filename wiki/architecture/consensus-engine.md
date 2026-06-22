---
title: Consensus Engine
type: architecture
date: 2026-06-22
tags: [consensus, regime-conditional, hurst, weights]
---

# Consensus Engine

`src/lib/analysis/consensus.ts` — 7-layer weighted fusion with regime-conditional weights.

## Layers
| Layer | Default Weight | Source |
|-------|---------------|--------|
| technical | 0.25 | RSI/MACD/EMA/Bollinger/VWAP vote count |
| orderbook | 0.15 | Binance depth imbalance |
| onchain | 0.10 | BTC hashrate trend (blockchain.info) |
| sentiment | 0.20 | News LLM analysis + funding rate adjustment |
| macro | 0.10 | DXY/VIX/Gold/Oil/Yields |
| fundamental | 0.10 | Dev activity, on-chain flows |
| intermarket | 0.10 | Cross-asset correlations |

## Regime-Conditional Weights (Hurst exponent)

`getRegimeWeights()` computes the Hurst exponent on log returns:
- **H > 0.55 (trending):** technical 0.25→0.35, sentiment 0.20→0.10 (momentum works, noise reduced)
- **H < 0.45 (mean-reverting):** technical 0.25→0.15, sentiment 0.20→0.25 (contrarian works, momentum fails)
- **0.45-0.55 (random walk):** default weights (no edge either way)

This prevents the structurally losing pattern of applying RSI mean-reversion logic in a trending market.

## Output
- `direction`: long (>+15) / short (<-15) / neutral
- `conviction`: 0-100 (|score|×0.6 + avgConfidence×0.4)
- `entryPrice`, `stopLoss` (entry - ATR×1.5), `takeProfit` (entry + ATR×3)
- `layers[]`: per-layer score + confidence + detail

See also: [[analysis-hurst]], [[regime-conditional-weights]], [[brain-engine]]

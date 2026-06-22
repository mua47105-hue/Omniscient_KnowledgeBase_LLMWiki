# OMNISCIENT Knowledge Base — Index

This is the content catalog for the OMNISCIENT knowledge base. Every wiki page is listed here with a link, one-line summary, and metadata.

## Architecture

- [[architecture-overview]] — System architecture: Next.js 16 + Prisma + SQLite, 32 pages, 44 API routes
- [[lazy-brain-engine]] — The autonomous orchestration layer that applies ponytail's ladder to token usage
- [[scheduler-tick-flow]] — The 12-step tick pipeline: grading → scan → triggers → self-tune
- [[llm-router]] — Multi-provider LLM router with auto-fallback + multi-key rotation
- [[consensus-engine]] — 7-layer weighted fusion with regime-conditional weights (Hurst exponent)
- [[huggingface-deployment]] — Docker deployment to HF Spaces (Dockerfile + CI/CD workflow)

## Entities (Modules)

- [[brain-state]] — In-memory singleton: running, mode, config, watch, stats, budget, triggers
- [[brain-engine]] — computeNoteworthiness, classifyRegime, dataSignature, gateDecide
- [[brain-selftune]] — Self-tuning thresholds from grading feedback
- [[brain-triggers]] — Cross-asset triggers (BTC/ETH → correlated alts)
- [[brain-news-triggers]] — News-event triggers (RSS keyword scan, 5-min cache)
- [[analysis-grading]] — Triple-barrier path-dependent grading (replaces point-in-time)
- [[analysis-cointegration]] — Engle-Granger ADF + half-life + z-score (own OLS, no deps)
- [[analysis-triple-barrier]] — López de Prado triple-barrier labeling (TP/SL/timeout)
- [[analysis-deflated-sharpe]] — Bailey-LdP DSR (multiple-testing correction)
- [[analysis-hurst]] — DFA-based Hurst exponent regime filter
- [[analysis-fear-greed-edge]] — Asymmetric F&G (momentum-long on greed streaks)
- [[risk-vol-targeting]] — E1 vol-targeting position sizing (Moreira-Muir 2017)
- [[market-deribit]] — E4 Deribit options (DVOL, 25Δ skew) + Binance Coin-M basis
- [[market-onchain]] — blockchain.info stats + hashrate-trend tracker
- [[market-devactivity]] — GitHub commit count + 7d delta trend
- [[market-coingecko]] — Trending + top markets
- [[market-reddit]] — Word-count sentiment (graceful 403)

## Concepts

- [[ponytail-ladder]] — Token-economy gate: budget→YAGNI→cache→cadence→analyze
- [[llm-circuit-breaker]] — Global cooldown (30s→60s→120s) on 429, prevents thundering-herd
- [[regime-conditional-weights]] — LAYER_WEIGHTS shift based on Hurst: trending vs mean-reverting
- [[triple-barrier-grading]] — Path-dependent grading: SL touched intra-window → wrong (was correct)
- [[fee-slippage-model]] — Backtest costs: 0.05% taker fee + 0.02% slippage per side
- [[dsr-gate]] — DSR < 0.95 = reject strategy (all 3 presets currently fail)

## Decisions

- [[decision-middleware-rewrite]] — Changed redirect(307) to rewrite(200) for HF Spaces health check
- [[decision-node20-runner]] — Switched from oven/bun:1 to node:20-slim for HF Spaces runner
- [[decision-single-stage-dockerfile]] — Simplified from multi-stage to single-stage Dockerfile
- [[decision-cache-bust-arg]] — ARG CACHE_BUST=<timestamp> forces HF to rebuild all layers

## Sources

- [[field-guide-vol2]] — "OMNISCIENT — Field Guide to Real Edge (Vol. 2)" — 12 edge suggestions
- [[ponytail-repo]] — github.com/DietrichGebert/ponytail — lazy senior dev token-economy pattern

## Test Coverage

- [[test-baseline]] — 47 tests across 8 files: indicators, consensus, vol_targeting, cointegration, hurst, triple-barrier, deflated-sharpe, correlation, atr-fallback, oos-backtest
- [[oos-backtest-harness]] — Synthetic 365-day klines, 70/30 train/test split, fee+slippage, DSR gate

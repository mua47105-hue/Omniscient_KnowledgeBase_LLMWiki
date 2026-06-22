---
title: System Architecture Overview
type: architecture
date: 2026-06-22
tags: [architecture, nextjs, prisma, sqlite]
---

# System Architecture

OMNISCIENT is a Next.js 16 App Router monolith with Prisma ORM (SQLite dev / PostgreSQL prod via Supabase).

## Tech Stack
- **Framework:** Next.js 16 (App Router, Turbopack, standalone output)
- **Language:** TypeScript 5
- **Styling:** Tailwind CSS 4 + shadcn/ui (New York)
- **Database:** Prisma 6 ORM (SQLite)
- **State:** @tanstack/react-query 5
- **Charts:** recharts + custom inline-SVG sparklines
- **Runtime:** Bun (dev), Node.js 20 (HF Spaces production)
- **Scheduler:** Bun mini-service on port 3042

## Structure
- 32 page routes (src/app/)
- 44 API routes (src/app/api/)
- 80 React components (src/components/)
- 34 lib modules (src/lib/)
- 47 characterization tests (src/lib/__tests__/)

## Call Chain
```
mini-services/scheduler (port 3042, 60s poll)
  → POST /api/scheduler/tick
    → gradeExpiredSignals() (triple-barrier path-dependent grading)
    → checkPriceAlerts()
    → runForcedAnalysis() (manual override queue)
    → checkNewsTriggers() (RSS keyword scan, 5-min cache)
    → runCryptoScan() → per-asset analyzeAsset():
        → fetch klines/orderbook/funding/ticker (Binance)
        → computeIndicators() (RSI/MACD/EMA/Bollinger/VWAP/ATR)
        → getOnchainTrend() (BTC hashrate trend)
        → computeConsensus() (7-layer + regime-conditional weights)
        → gateDecide() (ponytail ladder: budget→YAGNI→cache→cadence→analyze)
        → volTargetSize() (E1 position sizing)
        → db.signal.create() (with [trigger:...] + [vol-target:...] tags)
    → checkCrossAssetTriggers()
    → selfTune() (reads SignalOutcome grades, nudges thresholds)
    → recordSample() (token economy timeline)
```

See also: [[lazy-brain-engine]], [[scheduler-tick-flow]], [[consensus-engine]], [[llm-router]]

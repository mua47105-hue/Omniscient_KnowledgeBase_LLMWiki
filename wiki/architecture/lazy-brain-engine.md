---
title: The Lazy Brain Engine
type: architecture
date: 2026-06-22
tags: [brain, autonomous, token-economy, ponytail]
---

# The Lazy Brain

The Lazy Brain is the autonomous orchestration layer that governs whether and how deeply the LLM is consulted per asset per tick. It never silences a real signal — the deterministic consensus always runs; the brain only governs the LLM layer.

## The Gate (ponytail's ladder applied to token usage)

`gateDecide()` in `src/lib/brain/engine.ts` returns the first rung that holds:

1. **Budget** — if the rolling token budget is exhausted → skip LLM (free-tier safety net)
2. **YAGNI** — if the deterministic consensus is unanimous + high-conviction → skip LLM
3. **Cache** — if the market-data fingerprint (`dataSignature`) is unchanged → reuse the last verdict
4. **Cadence** — if nothing noteworthy + recently analyzed → skip
5. **Minimum** — only then call the LLM (tier 1 triage / tier 2 deep)

## State (`src/lib/brain/state.ts`)

In-memory singleton on `globalThis`:
- `running` / `mode` (pause/resume + auto/manual)
- `config` (gate thresholds: minNoteworthiness, highNoteworthiness, unanimousConviction, etc.)
- `watch: Map<symbol, AssetWatch>` (per-asset verdict, noteworthiness, regime, last action)
- `stats` (ticksTotal, llmCallsTotal, tokensUsed, tokensSaved, cacheHits, budgetSkips, triggers)
- `forceRunQueue: Map<symbol, source>` (manual/news/cross-asset override queue)
- `statsSamples` (ring buffer, capped 120 — sparkline timeline)
- `tuneEvents` (ring buffer, capped 50 — self-tune history)

## LLM Circuit-Breaker

When Pollinations 429s one asset, `recordLlmFailure()` trips a global cooldown (30s→60s→120s exponential backoff). Subsequent assets skip the LLM (`reason: 'llm-cooldown'`) — preventing the thundering-herd where 11 assets all fire 429'd requests simultaneously.

## Token Economy Results
- 3× token savings on a free stack
- 72% of tokens saved (not spent) vs gross
- Cache hits + unanimous-deterministic skips + budget guards

See also: [[ponytail-ladder]], [[llm-circuit-breaker]], [[brain-state]], [[brain-engine]]

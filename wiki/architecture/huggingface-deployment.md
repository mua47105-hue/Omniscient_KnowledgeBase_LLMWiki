---
title: HuggingFace Spaces Deployment
type: architecture
date: 2026-06-22
tags: [deployment, docker, huggingface, ci-cd]
---

# HuggingFace Spaces Deployment

## Dockerfile Evolution
1. ~~Multi-stage: oven/bun:1 + standalone server.js~~ — "Ready in 0ms", never served
2. ~~node:18-slim + standalone server.js~~ — same issue (HOSTNAME not 0.0.0.0)
3. ~~start.sh with HOSTNAME=0.0.0.0~~ — HF cached old layers, didn't rebuild
4. **Single-stage node:20-slim + `next start -H 0.0.0.0 -p 7860`** — works

## Key Fixes
1. **Middleware rewrite not redirect:** `NextResponse.redirect(307)` → `NextResponse.rewrite(200)`. HF health-checks GET / and got 307 → "not ready" → stuck. Rewrite serves /lock at / with 200 OK.
2. **node:20-slim not oven/bun:1:** Next.js standalone is designed for Node.js. Bun's compat mode doesn't properly bind HTTP server.
3. **Cache-bust ARG:** `ARG CACHE_BUST=<timestamp>` at top of Dockerfile forces HF to rebuild all layers.
4. **seed.cjs not seed.ts:** `bun run src/lib/db/seed.ts` fails because `@/lib/db` path alias isn't resolved by Bun outside Next.js. seed.cjs uses `require('@prisma/client')` directly.

## CI/CD
`.github/workflows/deploy-hf.yml` — on push to main:
1. Checks out GitHub repo
2. Clones HF Space repo
3. Copies source files (excluding node_modules, .next, db, .env)
4. Injects cache-bust ARG into Dockerfile
5. Commits + pushes to HF Space
6. HF auto-builds Dockerfile and deploys

## HF Space
- URL: https://tskkkk-omniscient.hf.space
- Port: 7860
- Login: `omniscient`
- Free tier (CPU basic)

See also: [[decision-middleware-rewrite]], [[decision-node20-runner]], [[decision-cache-bust-arg]]

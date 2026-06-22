---
title: Triple-Barrier Grading
type: concept
date: 2026-06-22
tags: [grading, triple-barrier, lopez-de-prado, path-dependent]
---

# Triple-Barrier Grading

`src/lib/analysis/grading.ts` — path-dependent signal grading using the triple-barrier method.

## The Defect (Fixed)

The old `evaluate()` checked only the final price at `expiresAt`:
- A long signal that touched its stop-loss intra-window and recovered above entry by expiry was graded **"correct"**
- This fed false positives into `selftune.ts`, which mutates live trading thresholds

## The Fix

`evaluateWithPath()` fetches 1h klines covering the signal's lifetime, walks the path, and uses `tripleBarrierLabel()` to determine which barrier was hit FIRST:
- **SL touched intra-window → wrong** (even if price recovered by expiry)
- **TP touched intra-window → correct** (even if price gave back gains)
- **Neither touched → point-in-time at timeout bar close**

Falls back to `evaluatePointInTime()` for non-crypto or kline fetch failure.

## Conservative Ordering
SL checked before TP on each bar — if both fall inside the same bar's range, SL wins. This biases labels toward pessimism (safe side for a self-grading loop).

## Impact
`selftune.ts` now receives accurate labels. The grading distribution shifts: fewer "partial", more "correct"/"wrong". Self-tuning nudges become directionally consistent.

See also: [[analysis-grading]], [[analysis-triple-barrier]], [[brain-selftune]]

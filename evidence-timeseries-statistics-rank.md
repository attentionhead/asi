# Time-series statistics rank-contract evidence

Baseline: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

Candidate: `62ce4ee7f78d704e4e2adc05682b89a2d638afdf`

Public surface: `alberta_framework.utils.compute_timeseries_statistics`

Documented contract: input shape `(n_seeds, n_steps)`; each returned array has
shape `(n_steps,)`.

## Deterministic baseline probe

| Input shape | Baseline behavior | Contract failure |
|---|---|---|
| `()` | `IndexError` at `shape[0]` | accidental internal exception |
| `(3,)` | returns three scalar values | trace collapsed into a seed sample |
| `(2, 3, 1)` | returns three `(3, 1)` arrays | non-vector output silently accepted |

For rank-one input `[0.2, 0.4, 0.8]`, baseline returned mean
`0.46666666666666673` and interval
`[-0.2922499400526853, 1.2255832733860188]`. The three time steps were treated
as three independent seeds and collapsed to a scalar measurement.

## Candidate behavior

Ranks zero, one, and three all raise a shape-bearing `ValueError` before seed
counting, finiteness checks, means, or confidence intervals. Valid rank-two
inputs retain the existing zero-seed, one-seed, non-finite, and Student-t
paths unchanged.

This is a validator-integrity Fix. It runs no benchmark, consumes no tuning or
evaluation seed, writes no output artifact, and makes no promotion claim.

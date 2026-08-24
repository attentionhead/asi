# Partial-window containment evidence

Contribution type: validator fix

Repository: `SlopDotCash/asi`

Parent: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

Candidate: `4370bb0e8c0050839c8e66b4586a1c80be97c433`

Declared run identity: `openai` / `gpt-5.6-sol` / `codex`

## Defect

The failed-shard validator checked each metric window against the environment's reward range
and lattice, but did not bind the window reward to the shard's phase or total reward. A record
with one accepted event could claim `reward_sum = 0.0` and a window `reward_sum = 1.0`. The
validator accepted that contradiction for both `switching_two_state` and `riverswim`.

## Red reproduction at the parent

A temporary detached worktree at the parent received only the new parametrized regression
test. The production fix was absent.

```text
/Users/zxmini/Sandbox/Slopcash/asi/.venv/bin/python -m pytest \
  tests/test_reference_life_scorecard.py::test_failed_shard_rejects_window_reward_exceeding_partial_total \
  -q -o addopts=""

FF [100%]
2 failed in 1.68s

Both cases failed with: Failed: DID NOT RAISE ValueError
```

The temporary worktree was removed after reproduction.

## Fix

`_validate_metric_window` already returns its validated finite reward sum. The partial-outcome
validator now accumulates those sums by switching phase, or across the stationary RiverSwim
windows, and rejects a sum greater than the corresponding recorded phase reward. The tolerance
is `1e-9`, matching the validator's existing reward arithmetic tolerance.

This check relies on nonnegative canonical rewards. The fixed scorecard windows are disjoint in
the canonical plan.

## Green verification at the candidate

```text
.venv/bin/python -m pytest tests/test_reference_life_scorecard.py -q -o addopts=""
66 passed, 1 skipped in 24.06s

.venv/bin/python -m ruff check .
All checks passed!

git diff --check origin/main...HEAD
exit 0
```

Full-tree mypy checked 224 source files and retained one pre-existing current-main error:

```text
alberta_framework/evaluation/bounded_elastic_matched_runner.py:1111:
error: Module has no attribute "O_TMPFILE" [attr-defined]
Found 1 error in 1 file (checked 224 source files)
```

That file is unchanged from `origin/main`.

A broad fast-lane run reached 1,697 passes and 34 skips before it was stopped after the same 11
Linux-only immutable-publication tests failed on macOS. Open issue #2251 tracks those failures.
This branch does not touch that module or its tests.

`alberta-evidence-status` retained exit 2 from current registered-source drift. The scorecard
fix does not alter any registered evidence source, artifact, threshold, seed, or promoted claim.

## Claim scope

This is a measurement-integrity fix. It reports no benchmark improvement, uses no tuning or
evaluation seeds, writes no output artifact under `outputs/`, and makes no scientific promotion
claim.

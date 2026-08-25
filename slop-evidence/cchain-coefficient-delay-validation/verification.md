# C-CHAIN coefficient-delay receipt validation

Implementation head: `58242bd20b56aa4196331198300e9ba0b23db638`

Exact base: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

This is deterministic Fix-lane validator evidence. No benchmark campaign,
development seed, output artifact, threshold, or promoted evaluation was run
or changed.

## Reproduction

A registered `cchain_full` receipt was built from a one-task, four-observation
CI slice. The run recorded two active diagnostics, fewer than the frozen
coefficient delay of ten, and correctly emitted `final_coefficient = 1.0`.
Changing only that field to `2.0` remained accepted by the exact-base public
validator.

The failing-test-first regression produced one failure because the expected
`ValueError` was not raised.

## Candidate verification

- `pytest tests/test_cchain_receipt_validation.py tests/test_cchain_ipmnist.py -q`
  — 17 passed.
- `pytest tests/test_ipmnist_screening.py tests/test_replay_frozen_ipmnist.py tests/test_noise_curvature_ipmnist.py tests/test_bounded_elastic_ipmnist.py -q`
  — 371 passed.
- `ruff check .` — passed.
- strict mypy on both changed files — passed.
- `git diff --check` — passed.

Repository-wide mypy checked 224 source files and retained only the unchanged
exact-base macOS stub error for `os.O_TMPFILE` in
`alberta_framework/evaluation/bounded_elastic_matched_runner.py`; it is not
claimed green.

The changed source is not registered in the five-claim frozen evidence
manifest. Exact open issue and pull-request searches found no owner for this
validator invariant. Open PR #2386 concerns NTK-rank computation in the
benchmark source and does not modify the evaluation validator or this new
receipt-only regression file.

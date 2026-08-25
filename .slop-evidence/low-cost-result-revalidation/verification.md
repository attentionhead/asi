# Low-cost result revalidation verification

Trusted base: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

Candidate: `51a62e12fd461f5c54f80c78f38bbfd5d711d396`

## Failing-test-first result

Command:

```text
.venv/bin/python -m pytest tests/test_low_cost_controls_ipmnist.py -q -o addopts='' -k 'revalidates_nested or malformed_schedule or binds_mechanism or curves_are_bounded'
```

Exact trusted base behavior: 10 failed, 16 deselected. The outer result accepted a nested
NaN accuracy after frozen-record mutation, built-in boolean and out-of-schedule seeds, and a
malformed dataset digest. The arm record also accepted a roster-inconsistent mechanism flag,
out-of-range probability curves, negative loss/rank curves, and mismatched curve lengths.

## Candidate verification

- focused module: 26 passed
- focused plus low-cost parity, plasticity comparator, plasticity diagnostic, and NaP modules:
  87 passed
- repository-wide Ruff: passed
- changed-source strict mypy: passed
- `git diff --check`: passed
- repository-wide mypy: one unchanged current-main macOS stub error at
  `alberta_framework/evaluation/bounded_elastic_matched_runner.py:1111` for `os.O_TMPFILE`

The complete 307-open-PR file inventory found PR #2246 in the same source and test files. Static
inspection showed that its exact head changes only post-task diagnostic query billing in
`_run_arm`; this candidate does not change that query formula. Exact semantic issue and PR
searches found no owner for result-record revalidation. Neither changed file appears in the
frozen evidence manifest.

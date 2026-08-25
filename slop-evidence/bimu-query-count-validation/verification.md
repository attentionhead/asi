# BiMU zero-threshold query-count validation

Implementation head: `7ec698aae7cf32622b1a4f9e33c44736954f0b71`

Verified base: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

## Failing-test-first reproduction

The exact-base runner produced a 20-observation receipt with 20 label queries. A reconstructed
receipt set `label_queries` and `optimizer_updates` to zero and adjusted
`model_forward_queries` to the validator's corresponding formula value, 90. The validator
accepted that receipt even though the bound configuration has `query_threshold=0.0`, for which
`variation_ratio >= query_threshold` is true for every observation.

Command:

```text
.venv/bin/python -m pytest tests/test_bimu.py -q -k zero_threshold
```

Exact-base result: 1 failed, 25 deselected. The failure was `DID NOT RAISE ValueError`.

## Candidate verification

```text
.venv/bin/python -m pytest tests/test_bimu.py -q -k zero_threshold
```

Result: 1 passed, 25 deselected.

```text
.venv/bin/python -m pytest tests/test_bimu.py tests/test_bimu_matched_campaign.py tests/test_bimu_matched_nonpromoting.py -q
```

Result: 45 passed, 24 skipped.

```text
.venv/bin/python -m ruff check .
```

Result: passed.

```text
.venv/bin/python -m mypy alberta_framework/benchmarks/bimu.py
```

Result: passed for the changed source.

```text
.venv/bin/python -m mypy
```

Result: retained the unchanged exact-base macOS stub error for `os.O_TMPFILE` in
`alberta_framework/evaluation/bounded_elastic_matched_runner.py`; repository-wide mypy is not
claimed green.

```text
git diff --check
```

Result: passed.

No benchmark campaign, development seed, threshold, output artifact, or registered evidence
source was changed or executed.

# L2-ER accuracy-lattice verification

Implementation head: `9eba186c595d8421b4ca6d46f44f7934afd4f47a`

Base head: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

The failing-test-first regression replaced a valid 100-observation receipt's
`mean_online_accuracy` with `0.6001`. On the exact base, the targeted test failed
because `validate_l2er_development_result` did not raise.

At the implementation head:

- `pytest tests/test_l2er_ipmnist.py tests/test_l2er_matched_development.py -q`:
  32 passed.
- `pytest tests/test_ipmnist_screening.py -q`: 340 passed.
- `ruff check .`: passed.
- strict mypy on
  `alberta_framework/evaluation/l2er_ipmnist_nonpromoting.py`: passed.
- `git diff --check`: passed.

Repository-wide mypy was also run. It retained only the unchanged exact-base
macOS stub error for `os.O_TMPFILE` in
`alberta_framework/evaluation/bounded_elastic_matched_runner.py`; it is not
claimed green.

No benchmark was run, and no development or scientific result is claimed.

# Action-latent return validation evidence

Head: `0eaac3b0780438d8a2bad0783d01eb6a384277dc`

This is a measurement-integrity fix for the permanently nonpromoting
action-conditioned latent development lane. It changes no arm, seed, threshold,
environment, or stored result.

## Reproduction on exact main

Base: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

A canonical eight-step result reported `return_sum=7.0` and
`late_return_sum=4.0`. Replacing those fields with `-1.0` and `9.0`, while
leaving the action and reward trace hashes unchanged, still passed
`validate_action_latent_payload`.

The environment's frozen payoff matrices contain only `0.0` and `1.0`.
Therefore a full return must be an integer in `[0, steps]`, the late return
must be an integer in `[0, phase_length]`, and the late return cannot exceed
the full return.

The failing-test-first regression checked four corruptions:

- negative full return;
- fractional full return;
- late return larger than its window;
- individually bounded full and late returns that violate subset containment.

All four were accepted on the base and rejected at the measured head.

## Verification

```text
.venv/bin/python -m pytest tests/test_action_conditioned_latent.py -q -o addopts=''
17 passed

.venv/bin/python -m pytest tests/test_action_conditioned_latent.py tests/test_closed_loop_env.py tests/test_closed_loop_payoff_shape_hostile_validation.py -q -o addopts=''
134 passed

.venv/bin/python -m ruff check .
All checks passed!

.venv/bin/python -m mypy alberta_framework/benchmarks/action_conditioned_latent.py
Success: no issues found in 1 source file

git diff --check
passed
```

Repository-wide mypy retains the unchanged macOS stub error for
`os.O_TMPFILE` in `bounded_elastic_matched_runner.py`; it is not claimed green.

## Overlap check

A complete inventory of all 304 open PR file lists found only PR #2258 in the
same test module. That PR adds a separate transition-identifiability test and
does not touch the validator source or this return-lattice invariant. Exact
semantic searches for `late_return_sum` found no open issue or PR.
